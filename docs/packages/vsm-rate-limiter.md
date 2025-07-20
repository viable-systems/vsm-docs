# VSM Rate Limiter

A sophisticated rate limiting library for Elixir that implements Viable System Model (VSM) principles with variety attenuation, pluggable adapters, and algedonic signaling.

## Overview

The VSM Rate Limiter provides intelligent rate limiting based on cybernetic principles, specifically implementing Ashby's Law of Requisite Variety. It ensures that the variety (complexity) of incoming requests matches the system's capacity to handle them.

## Key Features

- **VSM-based Architecture**: Rate limiting organized by VSM subsystems (S1-S5)
- **Variety Attenuation**: Implements Ashby's Law of Requisite Variety
- **Multiple Adapters**: Support for token bucket, ExRated, and Hammer
- **Algedonic System**: Critical alerts and pain/pleasure signals
- **Subsystem-specific Limits**: Different rate limits for each VSM subsystem
- **Telemetry Integration**: Built-in metrics and monitoring

## Installation

Add `vsm_rate_limiter` to your dependencies:

```elixir
def deps do
  [
    {:vsm_rate_limiter, github: "viable-systems/vsm-rate-limiter"}
  ]
end
```

## Basic Usage

### Configure a Subsystem

```elixir
VsmRateLimiter.configure_subsystem(:s1_environment, 
  rate_limit: {100, :requests_per_minute},
  algedonic_threshold: 0.8
)
```

### Check Rate Limit

```elixir
case VsmRateLimiter.check_rate(:s1_environment, "user_123") do
  {:ok, remaining} -> 
    # Request allowed
    process_request()
  {:error, :rate_limited} ->
    # Request rejected - variety exceeded
    {:error, :too_many_requests}
end
```

## Adapter Selection

The library supports three rate limiting strategies:

```elixir
# Use custom token bucket (default)
VsmRateLimiter.use_adapter(:token_bucket)

# Use ExRated library
VsmRateLimiter.use_adapter(:ex_rated)

# Use Hammer library
VsmRateLimiter.use_adapter(:hammer)
```

## VSM Subsystem Configuration

Each VSM subsystem has different characteristics and default rate limits:

| Subsystem | Default Limit | Purpose |
|-----------|---------------|---------|
| `:s1_environment` | 1000/min | Environment monitoring (highest volume) |
| `:s2_coordination` | 500/min | Coordination between units |
| `:s3_control` | 300/min | Operational control |
| `:s3_star_audit` | 100/min | Audit and compliance (critical) |
| `:s4_intelligence` | 400/min | Intelligence gathering |
| `:s4_star_research` | 200/min | Research and development |
| `:s5_policy` | 50/min | Policy decisions (lowest volume, highest priority) |

## Algedonic Integration

The rate limiter includes a sophisticated algedonic (pain/pleasure) signaling system:

```elixir
# Register a custom alert handler
VsmRateLimiter.Algedonic.register_handler(fn alert, severity, metadata ->
  case severity do
    :critical -> 
      # Emergency response - system in pain
      trigger_circuit_breaker(alert.subsystem)
    :high ->
      # Warning - approaching limits
      notify_operations_team(alert)
    _ ->
      # Log for analysis
      Logger.info("Algedonic signal", alert: alert)
  end
end)
```

## Telemetry Events

The library emits telemetry events compatible with vsm-telemetry:

```elixir
# Rate limit check events
[:vsm_rate_limiter, :request, :allowed]
[:vsm_rate_limiter, :request, :rejected]

# Token bucket events
[:vsm_rate_limiter, :bucket, :refill]

# Algedonic events
[:vsm_rate_limiter, :algedonic, :alert]
```

## Integration with VSM Ecosystem

### With vsm-telemetry

```elixir
# Telemetry events are automatically forwarded
:telemetry.attach(
  "rate-limiter-metrics",
  [:vsm_rate_limiter, :request, :allowed],
  &VsmTelemetry.MetricsCollector.handle_event/4,
  nil
)
```

### With vsm-goldrush

```elixir
# Convert rate limiter events for pattern detection
event = %{
  type: "rate_limit_check",
  subsystem: "s1_environment",
  result: "rejected",
  timestamp: DateTime.utc_now()
}

VsmGoldrush.process_event(:rate_limit_pattern, event)
```

## Architecture

The VSM Rate Limiter implements key cybernetic principles:

1. **Variety Attenuation**: Reduces the variety of incoming requests to match system capacity
2. **Hierarchical Control**: Different rate limits based on VSM subsystem hierarchy
3. **Algedonic Signaling**: Pain/pleasure signals for critical events
4. **Adaptive Behavior**: Dynamic rate limiting based on system load

### Variety Attenuation Algorithm

The rate limiter implements Ashby's Law by dynamically adjusting limits based on:

- System load estimation
- Subsystem priority (S5 > S4 > S3 > S2 > S1)
- Current variety levels

```elixir
# Automatic variety attenuation
config = VsmRateLimiter.SubsystemManager.apply_variety_attenuation(
  :s1_environment,
  base_config
)
```

## Configuration

### Application Configuration

```elixir
# config/config.exs
config :vsm_rate_limiter,
  adapter: :token_bucket,
  default_rate_limit: {100, :requests_per_minute},
  algedonic_threshold: 0.8

# Configure Hammer backend if using
config :hammer,
  backend: {Hammer.Backend.ETS, [
    expiry_ms: :timer.hours(1),
    cleanup_interval_ms: :timer.minutes(5)
  ]}
```

### Runtime Configuration

```elixir
# Configure specific subsystems at runtime
for subsystem <- [:s1_environment, :s2_coordination, :s3_control] do
  VsmRateLimiter.configure_subsystem(subsystem,
    rate_limit: {custom_limit(subsystem), :requests_per_minute},
    algedonic_threshold: 0.9
  )
end
```

## Advanced Usage

### Custom Token Bucket Parameters

```elixir
VsmRateLimiter.configure_subsystem(:s1_environment,
  rate_limit: {1000, :requests_per_minute},
  refill_rate: 16.67,  # tokens per second
  burst_size: 1200     # allow brief bursts
)
```

### Monitoring Integration

```elixir
defmodule MyApp.RateLimiterMonitor do
  use GenServer
  
  def init(_) do
    # Attach to all rate limiter events
    :telemetry.attach_many(
      "rate-limiter-monitor",
      [
        [:vsm_rate_limiter, :request, :allowed],
        [:vsm_rate_limiter, :request, :rejected],
        [:vsm_rate_limiter, :algedonic, :alert]
      ],
      &handle_event/4,
      nil
    )
    {:ok, %{}}
  end
  
  def handle_event([:vsm_rate_limiter, :request, :rejected], _, metadata, _) do
    # Track rejections by subsystem
    StatsD.increment("rate_limiter.rejected.#{metadata.subsystem}")
  end
end
```

## Testing

```bash
mix test
```

The library includes comprehensive tests for:
- Token bucket algorithm
- Adapter integrations
- Algedonic signaling
- Subsystem configurations

## References

- [Viable System Model](https://en.wikipedia.org/wiki/Viable_system_model)
- [Ashby's Law of Requisite Variety](https://en.wikipedia.org/wiki/Variety_(cybernetics)#Law_of_requisite_variety)
- [VSM Documentation](https://viable-systems.github.io/vsm-docs/)