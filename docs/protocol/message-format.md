# VSM Protocol: Message Format Specification

## Overview

The VSM Protocol defines a standardized message format for communication between all subsystems and components in a Viable System. This format ensures consistent, reliable, and traceable inter-subsystem communication while supporting the cybernetic principles of the VSM.

## Core Message Structure

### Standard Message Format

```elixir
%VSMCore.Shared.Message{
  id: binary(),           # Unique message identifier
  from: atom(),          # Source subsystem/component
  to: atom(),            # Destination subsystem/component  
  channel: atom(),       # Communication channel type
  type: atom(),          # Message type/action
  payload: map(),        # Message data/parameters
  timestamp: DateTime.t(), # Message creation time
  metadata: map()        # Additional message metadata
}
```

### Field Specifications

#### `id` (Required)
- **Type**: Binary string
- **Format**: UUID v4 or custom identifier
- **Purpose**: Unique identification for message tracing and correlation
- **Example**: `"msg_a1b2c3d4e5f6"`

#### `from` (Required)
- **Type**: Atom
- **Format**: Subsystem identifier or component name
- **Purpose**: Identifies the message originator
- **Valid Values**: `:system1`, `:system2`, `:system3`, `:system4`, `:system5`, `:component_name`

#### `to` (Required)
- **Type**: Atom
- **Format**: Subsystem identifier or component name
- **Purpose**: Identifies the message destination
- **Valid Values**: Same as `from` field

#### `channel` (Required)
- **Type**: Atom
- **Format**: Channel type identifier
- **Purpose**: Determines routing and processing behavior
- **Valid Values**: See [Channel Types](#channel-types)

#### `type` (Required)
- **Type**: Atom
- **Format**: Action or message type identifier
- **Purpose**: Defines the expected action or message category
- **Examples**: `:command`, `:alert`, `:status_update`, `:resource_request`

#### `payload` (Required)
- **Type**: Map
- **Format**: Key-value pairs with message data
- **Purpose**: Contains the actual message content and parameters
- **Validation**: Must be serializable (JSON-compatible)

#### `timestamp` (Required)
- **Type**: DateTime struct
- **Format**: ISO 8601 compatible
- **Purpose**: Message creation time for temporal analysis
- **Timezone**: UTC recommended

#### `metadata` (Optional)
- **Type**: Map
- **Format**: Key-value pairs with additional context
- **Purpose**: Routing hints, priority, correlation data
- **Common Keys**: `:priority`, `:correlation_id`, `:trace_id`, `:expires_at`

## Channel Types

### Command Channel (`:command_channel`)
Used for direct commands and control messages between subsystems.

```elixir
%Message{
  from: :system3,
  to: :system1,
  channel: :command_channel,
  type: :execute_operation,
  payload: %{
    operation: :scale_unit,
    unit_id: "worker_pool_a",
    target_capacity: 150
  },
  metadata: %{
    priority: :high,
    timeout: 5000
  }
}
```

### Coordination Channel (`:coordination_channel`)
Used for anti-oscillatory coordination between operational units.

```elixir
%Message{
  from: :system2,
  to: :system1,
  channel: :coordination_channel,
  type: :coordinate_resources,
  payload: %{
    units: [:unit_a, :unit_b],
    resource: :shared_database,
    allocation: %{unit_a: 0.6, unit_b: 0.4}
  }
}
```

### Audit Channel (`:audit_channel`)
Used for System 3* audit and monitoring communications.

```elixir
%Message{
  from: :system3_star,
  to: :system1,
  channel: :audit_channel,
  type: :audit_request,
  payload: %{
    audit_type: :performance,
    scope: [:unit_metrics, :resource_usage],
    time_window: {:hours, 24}
  }
}
```

### Algedonic Channel (`:algedonic_channel`)
Used for emergency alerts and pain/pleasure signals.

```elixir
%Message{
  from: :system1,
  to: :system5,
  channel: :algedonic_channel,
  type: :alert,
  payload: %{
    severity: :critical,
    signal_type: :pain,
    source: "database_connection_pool",
    description: "Connection pool exhausted",
    metrics: %{
      current_connections: 100,
      max_connections: 100,
      waiting_requests: 250
    }
  },
  metadata: %{
    bypass_hierarchy: true,
    requires_immediate_action: true
  }
}
```

### Resource Bargain Channel (`:resource_bargain_channel`)
Used for resource allocation negotiations between S1 and S3.

```elixir
%Message{
  from: :system1,
  to: :system3,
  channel: :resource_bargain_channel,
  type: :resource_request,
  payload: %{
    resource_type: :cpu_allocation,
    current_allocation: 0.4,
    requested_allocation: 0.7,
    justification: "Increased load from new customer segment",
    business_impact: :high
  }
}
```

## Message Types by Context

### Command Types
- `:execute` - Execute a specific operation
- `:configure` - Change system configuration
- `:start` / `:stop` - Lifecycle control
- `:scale` - Scaling operations

### Query Types
- `:status_request` - Request current status
- `:metrics_request` - Request performance metrics
- `:health_check` - Health verification
- `:capacity_inquiry` - Capacity information

### Alert Types
- `:emergency` - Critical system alerts
- `:warning` - Non-critical alerts
- `:info` - Informational messages
- `:recovery` - Recovery notifications

### Coordination Types
- `:sync_request` - Synchronization requests
- `:load_balance` - Load balancing coordination
- `:anti_oscillation` - Oscillation prevention
- `:resource_coordination` - Resource sharing

## Message Validation Rules

### Required Field Validation
```elixir
def validate_message(%Message{} = message) do
  with :ok <- validate_required_fields(message),
       :ok <- validate_subsystem_identifiers(message),
       :ok <- validate_channel_type(message),
       :ok <- validate_timestamp(message),
       :ok <- validate_payload_format(message) do
    {:ok, message}
  else
    {:error, reason} -> {:error, reason}
  end
end
```

### Subsystem Identifier Rules
- Must be valid VSM subsystem (`:system1` through `:system5`)
- Or registered component name
- Cannot be empty or nil

### Channel Type Rules
- Must be one of the defined channel types
- Channel must be appropriate for source/destination pair
- Some channels have restricted access (e.g., algedonic)

### Payload Validation
- Must be a valid Elixir map
- All values must be JSON-serializable
- Required payload fields depend on message type
- Size limits may apply (configurable)

## Message Routing

### Routing Rules

1. **Direct Routing**: Point-to-point between subsystems
2. **Broadcast Routing**: One-to-many for announcements
3. **Hierarchical Routing**: Following VSM command structure
4. **Emergency Routing**: Algedonic bypass routing

### Routing Algorithm

```elixir
def route_message(%Message{} = message) do
  case message.channel do
    :algedonic_channel ->
      # Emergency bypass - direct to System 5
      route_emergency(message)
      
    :command_channel ->
      # Hierarchical routing through command structure
      route_hierarchical(message)
      
    :coordination_channel ->
      # Peer-to-peer coordination routing
      route_coordination(message)
      
    _ ->
      # Standard point-to-point routing
      route_direct(message)
  end
end
```

## Message Correlation

### Correlation Patterns

#### Request-Response Correlation
```elixir
# Request
request = %Message{
  id: "req_123",
  type: :status_request,
  # ... other fields
}

# Response
response = %Message{
  id: "resp_456", 
  type: :status_response,
  metadata: %{
    correlation_id: "req_123",
    request_timestamp: request.timestamp
  }
  # ... other fields
}
```

#### Conversation Tracking
```elixir
conversation_id = "conv_#{:crypto.strong_rand_bytes(8) |> Base.encode16()}"

# All messages in conversation include this ID
metadata: %{
  conversation_id: conversation_id,
  sequence_number: 1
}
```

## Performance Considerations

### Message Size Limits
- **Standard**: 64KB payload recommended
- **Large**: Up to 1MB with performance impact
- **Streaming**: For larger data, use streaming protocols

### Batching Support
```elixir
%Message{
  type: :batch,
  payload: %{
    batch_id: "batch_789",
    messages: [message1, message2, message3],
    batch_size: 3
  }
}
```

### Compression
- Automatic compression for large payloads
- Configurable compression algorithms
- Maintains message structure integrity

## Security Considerations

### Message Authentication
- Optional message signing for security-critical systems
- Cryptographic signatures in metadata
- Public key infrastructure support

### Access Control
- Channel-based access restrictions
- Subsystem permission validation
- Rate limiting per source

### Audit Trail
- All messages logged for compliance
- Immutable message history
- Configurable retention policies

## Implementation Examples

### Creating Messages
```elixir
# Simple command message
message = VSMCore.Shared.Message.command(
  :system3,
  :system1, 
  :scale_operation,
  %{unit_id: "workers", target: 10}
)

# Algedonic alert
alert = VSMCore.Shared.Message.algedonic(
  :system1,
  %{
    severity: :critical,
    signal: :pain,
    source: "payment_processor",
    failure_rate: 0.15
  }
)

# Status query
query = VSMCore.Shared.Message.query(
  :system4,
  :system1,
  :performance_metrics,
  %{time_window: {:minutes, 15}}
)
```

### Message Handling
```elixir
defmodule MySubsystem do
  def handle_message(%Message{type: :command} = message) do
    execute_command(message.payload)
    {:ok, :executed}
  end
  
  def handle_message(%Message{type: :query} = message) do
    response_data = gather_response_data(message.payload)
    
    response = Message.response(
      message,
      :query_response,
      response_data
    )
    
    send_message(response)
    {:ok, :responded}
  end
  
  def handle_message(%Message{channel: :algedonic_channel} = message) do
    # Handle emergency alerts
    escalate_alert(message)
    {:ok, :escalated}
  end
end
```

## Versioning and Evolution

### Protocol Versioning
- Semantic versioning for protocol changes
- Backward compatibility requirements
- Migration paths for breaking changes

### Message Format Evolution
- Optional fields for new capabilities
- Graceful degradation for unknown fields
- Version negotiation between components

---

*This specification defines the core communication protocol enabling the VSM ecosystem.* 📨