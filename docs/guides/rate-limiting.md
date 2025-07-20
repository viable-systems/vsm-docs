# Rate Limiting in VSM Systems

This guide explains how to implement rate limiting in your VSM applications using the `vsm-rate-limiter` package, which provides variety attenuation based on cybernetic principles.

## Why Rate Limiting in VSM?

In the Viable System Model, **variety** refers to the number of possible states a system can have. Ashby's Law of Requisite Variety states that "only variety can destroy variety" - meaning a control system must have as much variety as the system it controls.

Rate limiting serves as a **variety attenuator**, reducing incoming request variety to match the system's capacity to handle it.

## Installation

Add the rate limiter to your VSM application:

```elixir
# mix.exs
def deps do
  [
    {:vsm_rate_limiter, github: "viable-systems/vsm-rate-limiter"}
  ]
end
```

## Basic Integration

### 1. Application Setup

Add the rate limiter to your supervision tree:

```elixir
# lib/my_vsm_app/application.ex
def start(_type, _args) do
  children = [
    # Your VSM subsystems
    MyVsmApp.System1.Supervisor,
    MyVsmApp.System2.Supervisor,
    # ... other subsystems
    
    # Start rate limiter after subsystems
    VsmRateLimiter.Application
  ]
  
  opts = [strategy: :one_for_one, name: MyVsmApp.Supervisor]
  Supervisor.start_link(children, opts)
end
```

### 2. Protecting Subsystems

Each VSM subsystem should implement rate limiting at its boundary:

```elixir
defmodule MyVsmApp.System1.Implementation do
  @moduledoc """
  System 1 - Operations implementation with rate limiting
  """
  
  def handle_operation(operation_id, params) do
    # Check rate limit before processing
    case VsmRateLimiter.check_rate(:s1_environment, operation_id) do
      {:ok, remaining} ->
        # Process the operation
        result = perform_operation(params)
        
        # Emit telemetry
        :telemetry.execute(
          [:vsm, :system1, :operation],
          %{count: 1, remaining_quota: remaining},
          %{operation_id: operation_id}
        )
        
        {:ok, result}
        
      {:error, :rate_limited} ->
        # Variety exceeded - reject request
        :telemetry.execute(
          [:vsm, :system1, :variety_exceeded],
          %{count: 1},
          %{operation_id: operation_id}
        )
        
        {:error, :too_many_requests}
    end
  end
end
```

### 3. Channel Protection

VSM channels should also implement rate limiting:

```elixir
defmodule MyVsmApp.Channels.Command do
  @moduledoc """
  Command channel with rate limiting
  """
  
  def send_command(from_subsystem, to_subsystem, command) do
    # Rate limit by source subsystem
    case VsmRateLimiter.check_rate(from_subsystem, "#{from_subsystem}:commands") do
      {:ok, _} ->
        # Forward command
        dispatch_command(to_subsystem, command)
        
      {:error, :rate_limited} ->
        # Command channel saturated
        trigger_algedonic_signal(:command_channel_saturated, from_subsystem)
        {:error, :channel_saturated}
    end
  end
end
```

## Advanced Patterns

### Hierarchical Rate Limiting

VSM's hierarchical structure allows for cascading rate limits:

```elixir
defmodule MyVsmApp.RateLimitingStrategy do
  @doc """
  Apply hierarchical rate limiting based on VSM structure
  """
  def check_hierarchical_limit(subsystem, identifier, operation) do
    # Check operation-specific limit first
    with {:ok, _} <- VsmRateLimiter.check_rate(subsystem, "#{identifier}:#{operation}"),
         # Then check subsystem-level limit
         {:ok, _} <- VsmRateLimiter.check_rate(subsystem, identifier),
         # Finally check system-wide limit
         {:ok, _} <- VsmRateLimiter.check_rate(:system_wide, "global") do
      :ok
    else
      {:error, :rate_limited} -> {:error, :rate_limited}
    end
  end
end
```

### Algedonic Integration

Connect rate limiting to your algedonic system:

```elixir
defmodule MyVsmApp.AlgedonicIntegration do
  def setup do
    # Register handler for rate limiter alerts
    VsmRateLimiter.Algedonic.register_handler(&handle_rate_limit_alert/3)
  end
  
  defp handle_rate_limit_alert(alert, severity, metadata) do
    case severity do
      :critical ->
        # System 5 intervention needed
        MyVsmApp.System5.Policy.emergency_variety_reduction(alert.subsystem)
        
      :high ->
        # System 3 needs to reallocate resources
        MyVsmApp.System3.Control.adjust_resource_allocation(alert.subsystem)
        
      :medium ->
        # System 2 should dampen oscillations
        MyVsmApp.System2.Coordination.increase_damping(alert.subsystem)
        
      :low ->
        # Log for System 4 analysis
        MyVsmApp.System4.Intelligence.record_variety_event(alert)
    end
  end
end
```

### Dynamic Configuration

Adjust rate limits based on system state:

```elixir
defmodule MyVsmApp.AdaptiveRateLimiting do
  use GenServer
  
  def init(_) do
    # Monitor system metrics
    :timer.send_interval(60_000, :adjust_limits)
    {:ok, %{}}
  end
  
  def handle_info(:adjust_limits, state) do
    # Get current system load
    metrics = VsmTelemetry.get_system_metrics()
    
    # Adjust limits based on load
    for {subsystem, load} <- metrics.subsystem_loads do
      new_limit = calculate_adaptive_limit(subsystem, load)
      
      VsmRateLimiter.configure_subsystem(subsystem,
        rate_limit: {new_limit, :requests_per_minute}
      )
    end
    
    {:noreply, state}
  end
  
  defp calculate_adaptive_limit(subsystem, load) do
    base_limit = base_limit_for(subsystem)
    
    cond do
      load > 0.9 -> round(base_limit * 0.5)  # Reduce variety under high load
      load > 0.7 -> round(base_limit * 0.8)  # Moderate reduction
      load < 0.3 -> round(base_limit * 1.2)  # Allow more variety when idle
      true -> base_limit
    end
  end
end
```

## Monitoring Integration

### With vsm-telemetry

Rate limiter events integrate seamlessly with vsm-telemetry:

```elixir
# The rate limiter automatically emits these events:
[:vsm_rate_limiter, :request, :allowed]
[:vsm_rate_limiter, :request, :rejected]
[:vsm_rate_limiter, :algedonic, :alert]

# vsm-telemetry will collect and display these in dashboards
```

### With vsm-goldrush

Detect patterns in rate limiting:

```elixir
# Define a pattern for variety explosions
VsmGoldrush.compile_pattern(:variety_explosion, %{
  sequence: [
    %{event_type: "rate_limit_rejected", count: 10, window: 60},
    %{event_type: "algedonic_alert", severity: "high"}
  ]
})

# React to pattern matches
VsmGoldrush.on_match(:variety_explosion, fn event ->
  # Trigger emergency variety attenuation
  MyVsmApp.EmergencyControl.attenuate_all_subsystems()
end)
```

## Best Practices

1. **Start Conservative**: Begin with lower rate limits and increase based on observed capacity
2. **Monitor Algedonic Signals**: Pay attention to pain signals from the rate limiter
3. **Subsystem-Specific Limits**: S5 (Policy) should have lower limits than S1 (Operations)
4. **Use Telemetry**: Always emit telemetry events for rate limit decisions
5. **Implement Graceful Degradation**: Have fallback behaviors when rate limited

## Configuration Examples

### Development Environment

```elixir
# config/dev.exs
config :vsm_rate_limiter,
  adapter: :token_bucket,
  default_rate_limit: {1000, :requests_per_minute},
  algedonic_threshold: 0.9  # Higher threshold in dev
```

### Production Environment

```elixir
# config/prod.exs
config :vsm_rate_limiter,
  adapter: :hammer,  # Use Hammer for production performance
  default_rate_limit: {100, :requests_per_minute},
  algedonic_threshold: 0.7  # More sensitive in production

# Configure subsystem-specific limits
config :vsm_rate_limiter, :subsystem_limits, %{
  s1_environment: {1000, :requests_per_minute},
  s2_coordination: {500, :requests_per_minute},
  s3_control: {300, :requests_per_minute},
  s3_star_audit: {100, :requests_per_minute},
  s4_intelligence: {400, :requests_per_minute},
  s4_star_research: {200, :requests_per_minute},
  s5_policy: {50, :requests_per_minute}
}
```

## Troubleshooting

### Common Issues

1. **"Rate limited immediately"**: Check if you're using the correct identifier
2. **"Algedonic alerts not firing"**: Verify threshold configuration
3. **"Different behavior between adapters"**: Each adapter has slight timing differences

### Debug Mode

Enable debug logging:

```elixir
# Enable detailed logging
Logger.configure(level: :debug)

# Watch rate limiter events
:telemetry.attach(
  "debug-rate-limiter",
  [:vsm_rate_limiter, :request],
  fn event, measurements, metadata, _ ->
    IO.inspect({event, measurements, metadata}, label: "RateLimiter")
  end,
  nil
)
```

## References

- [VSM Rate Limiter Documentation](/packages/vsm-rate-limiter/)
- [Ashby's Law of Requisite Variety](https://en.wikipedia.org/wiki/Variety_(cybernetics))
- [VSM Architecture Overview](/overview/architecture/)