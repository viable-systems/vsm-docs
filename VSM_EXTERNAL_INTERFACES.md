# VSM External Interfaces

## Overview

The VSM External Interfaces package provides comprehensive protocol adapters for integrating external systems with the Viable System Model ecosystem. It supports multiple communication protocols while maintaining the VSM's cybernetic principles and channel architecture.

## Architecture

### Protocol Adapters

The system provides three primary protocol adapters:

#### 1. HTTP REST Adapter
- **Purpose**: RESTful API for synchronous request/response communication
- **Module**: `VsmExternalInterfaces.Adapters.HTTP`
- **Features**:
  - Health monitoring endpoints
  - System discovery and management
  - Message routing to VSM subsystems
  - Algedonic alert triggering
  - JSON request/response format

#### 2. WebSocket Adapter
- **Purpose**: Real-time bidirectional communication
- **Module**: `VsmExternalInterfaces.Adapters.WebSocket`
- **Features**:
  - Phoenix Channels for real-time messaging
  - System state subscriptions
  - Event streaming
  - Interactive command execution
  - Persistent connections with heartbeat

#### 3. gRPC Adapter
- **Purpose**: High-performance RPC communication
- **Module**: `VsmExternalInterfaces.Adapters.GRPC`
- **Features**:
  - Unary RPC for single request/response
  - Server streaming for event feeds
  - Client streaming for batch operations
  - Bidirectional streaming for interactive sessions
  - Protocol buffer definitions

### Core Components

#### Protocol Translation Layer
- **Module**: `VsmExternalInterfaces.Translators.JsonTranslator`
- Bidirectional conversion between external formats and VSM messages
- Automatic format detection and validation
- Preserves VSM channel semantics

#### VSM Integration Bridge
- **Module**: `VsmExternalInterfaces.Integrations.VsmBridge`
- Routes messages to appropriate VSM subsystems
- Manages system discovery and state
- Handles event subscriptions
- Implements algedonic signal propagation

## VSM Channel Mapping

External interfaces map to VSM channels as follows:

### Temporal Channel
- **HTTP**: `/systems/:id/messages` with type "temporal"
- **WebSocket**: `system:*` channel with temporal messages
- **gRPC**: `SendMessage` RPC with temporal channel

### Algedonic Channel
- **HTTP**: `/algedonic/alert` endpoint
- **WebSocket**: Direct algedonic event broadcast
- **gRPC**: Emergency signal streaming

### Command Channel
- **HTTP**: `/systems/:id/messages` with type "command"
- **WebSocket**: `command:execute` events
- **gRPC**: Command execution via interactive sessions

### Coordination Channel
- **HTTP**: Coordination messages via standard endpoints
- **WebSocket**: Peer-to-peer coordination events
- **gRPC**: Coordination through streaming

### Resource Bargain Channel
- **HTTP**: Resource allocation requests
- **WebSocket**: Resource negotiation events
- **gRPC**: Resource management RPCs

## Message Routing

The VSM Bridge automatically routes messages based on channel and type:

- **Algedonic signals** → System 5 (Policy) with emergency bypass
- **Command messages** → System 3 (Control)
- **Coordination messages** → System 2 (Anti-oscillation)
- **Intelligence data** → System 4 (Environmental scanning)
- **Operational messages** → System 1 (Operations)

## Configuration

### Environment Variables
```bash
HTTP_PORT=4000          # HTTP REST API port
WS_PORT=4001           # WebSocket port
GRPC_PORT=50051        # gRPC port
VSM_HOST=localhost     # VSM core host
VSM_PORT=9000          # VSM core port
```

### Application Configuration
```elixir
config :vsm_external_interfaces,
  adapters: %{
    http: %{
      timeout: 30_000,
      pool_size: 100
    },
    websocket: %{
      heartbeat_interval: 30_000
    },
    grpc: %{
      max_message_length: 4 * 1024 * 1024
    }
  }
```

## Usage Examples

### HTTP REST
```bash
# Send operational message
curl -X POST http://localhost:4000/systems/plant_1/messages \
  -H "Content-Type: application/json" \
  -d '{
    "type": "command",
    "channel": "temporal",
    "payload": {"action": "start_production"}
  }'

# Trigger algedonic alert
curl -X POST http://localhost:4000/algedonic/alert \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Critical failure detected",
    "source": "sensor_array",
    "severity": "critical"
  }'
```

### WebSocket
```javascript
const socket = new Phoenix.Socket("ws://localhost:4001/socket")
const channel = socket.channel("system:plant_1", {})

channel.on("event:received", event => {
  console.log("VSM Event:", event)
})

channel.push("message:send", {
  message: {
    type: "query",
    channel: "temporal",
    payload: {query: "status"}
  }
})
```

### gRPC
```elixir
{:ok, channel} = GRPC.Stub.connect("localhost:50051")

# Stream events
{:ok, stream} = VsmService.Stub.stream_events(channel, %{
  system_id: "plant_1",
  event_types: ["state_change", "alert"]
})

Enum.each(stream, fn {:ok, event} ->
  IO.inspect(event)
end)
```

## Integration with VSM Core

The external interfaces integrate seamlessly with:

- **vsm-core**: Message routing and subsystem communication
- **vsm-connections**: Connection pooling and management
- **vsm-event-bus**: Event distribution and subscriptions
- **vsm-security**: Authentication and authorization

## Performance Considerations

- HTTP adapter uses connection pooling for efficiency
- WebSocket maintains persistent connections with heartbeat
- gRPC provides the highest throughput with streaming
- All adapters include telemetry for monitoring
- Circuit breakers prevent cascade failures

## Security

- Transport-level encryption (TLS) support
- Authentication hooks for all protocols
- Rate limiting integration
- Audit logging for compliance
- Input validation and sanitization

## Monitoring

The external interfaces emit telemetry events:

- `:vsm_external_interfaces.http.message_sent`
- `:vsm_external_interfaces.websocket.connection_established`
- `:vsm_external_interfaces.grpc.stream_started`
- `:vsm_bridge.message_routed`

## Future Enhancements

- MQTT adapter for IoT integration
- GraphQL API for flexible queries
- OpenAPI specification generation
- WebRTC for peer-to-peer communication
- Message queuing integration (RabbitMQ, Kafka)