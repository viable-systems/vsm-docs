# Getting Started

Welcome! This guide will help you get up and running with the Viable Systems Model (VSM) implementation in Elixir.

## Prerequisites

Before you begin, ensure you have the following installed:

- **Elixir** 1.15 or higher
- **Erlang/OTP** 25 or higher
- **PostgreSQL** 14 or higher (for persistence)
- **Node.js** 18 or higher (for Phoenix assets)
- **Git** for version control

## Quick Start

Get a basic VSM system running in under 5 minutes:

```bash
# Install the VSM CLI
mix archive.install hex vsm_cli

# Create a new VSM project
mix vsm.new my_system
cd my_system

# Install dependencies
mix deps.get

# Create and migrate database
mix ecto.create
mix ecto.migrate

# Start the system
iex -S mix phx.server
```

Your VSM system is now running at [http://localhost:4000](http://localhost:4000)!

## What's Next?

<div class="grid cards" markdown>

-   :material-package-variant:{ .lg .middle } **Installation Guide**

    ---

    Detailed installation instructions for different platforms and configurations.

    [:octicons-arrow-right-24: Installation](installation.md)

-   :material-speedometer:{ .lg .middle } **Quick Start Tutorial**

    ---

    Build a simple VSM system in 15 minutes with our step-by-step tutorial.

    [:octicons-arrow-right-24: Quick Start](quick-start.md)

-   :material-cube-send:{ .lg .middle } **First VSM System**

    ---

    Create your first production-ready VSM system with all five subsystems.

    [:octicons-arrow-right-24: First System](first-system.md)

-   :material-book-open-page-variant:{ .lg .middle } **Examples**

    ---

    Explore real-world examples and use cases for VSM systems.

    [:octicons-arrow-right-24: Examples](examples.md)

</div>

## Learning Path

### For Beginners
1. Start with the [Quick Start Tutorial](quick-start.md)
2. Build your [First VSM System](first-system.md)
3. Explore the [Examples](examples.md)
4. Read about [Core Concepts](../overview/key-concepts.md)

### For Experienced Developers
1. Review the [Architecture](../overview/architecture.md)
2. Dive into [Subsystem Documentation](../subsystems/index.md)
3. Check out [Advanced Guides](../guides/index.md)
4. Explore the [API Reference](../api-reference/index.md)

## Common Use Cases

VSM is ideal for:

- **Organizational Management**: Model and manage complex organizations
- **Supply Chain Systems**: Coordinate multi-tier supply networks
- **IoT Networks**: Manage large-scale sensor and actuator networks
- **Microservices Architecture**: Coordinate distributed services
- **Smart Cities**: Integrate urban subsystems
- **Healthcare Systems**: Coordinate care delivery networks

## System Requirements

### Minimum Requirements
- 2 CPU cores
- 4GB RAM
- 10GB disk space
- PostgreSQL 14+

### Recommended for Production
- 8+ CPU cores
- 16GB+ RAM
- 100GB+ SSD storage
- PostgreSQL 15+ with replication
- Redis for caching
- Prometheus for monitoring

## Getting Help

### Documentation
- [Overview](../overview/index.md) - Understand VSM concepts
- [Subsystems](../subsystems/index.md) - Deep dive into each component
- [API Reference](../api-reference/index.md) - Complete API documentation
- [Guides](../guides/index.md) - Best practices and how-tos

### Community
- [GitHub Discussions](https://github.com/viable-systems/vsm-core/discussions)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/vsm-elixir)
- [Elixir Forum](https://elixirforum.com/tag/vsm)

### Support
- [Report Issues](https://github.com/viable-systems/vsm-core/issues)
- [Request Features](https://github.com/viable-systems/vsm-core/issues/new?template=feature_request.md)
- [Security Issues](mailto:security@viable-systems.org)

## Example: Simple Monitoring System

Here's a taste of what you can build:

```elixir
defmodule MySystem.Monitor do
  use VSM.S1.Operation
  
  @impl true
  def init(config) do
    schedule_monitoring()
    {:ok, %{threshold: config.threshold}}
  end
  
  @impl true
  def handle_environment(data, state) do
    if data.value > state.threshold do
      {:alert, :high_value, data}
    else
      {:ok, data}
    end
  end
end
```

## Project Structure

A typical VSM project has this structure:

```
my_system/
├── config/           # Configuration files
├── lib/
│   ├── my_system/    # Your system implementation
│   │   ├── s1/       # Operations subsystem
│   │   ├── s2/       # Coordination subsystem
│   │   ├── s3/       # Control subsystem
│   │   ├── s4/       # Intelligence subsystem
│   │   └── s5/       # Policy subsystem
│   └── my_system_web/ # Phoenix web interface
├── priv/             # Static assets and migrations
├── test/             # Test files
└── mix.exs           # Project configuration
```

Ready to build something amazing? Let's [get started](installation.md)!

---

*Next: [Installation](installation.md) →*