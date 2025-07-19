# VSM Documentation

This repository contains the comprehensive documentation for the Viable Systems Model (VSM) implementation in Elixir.

## Overview

The Viable Systems Model, developed by Stafford Beer, is a cybernetic approach to organizational design and management. This documentation covers our Elixir implementation of VSM, including all core subsystems (S1-S5) and supporting components like the Algedonic Channel and our novel Temporal Variety Channel.

## Quick Start

### Prerequisites

- Python 3.8 or higher
- pip (Python package manager)

### Local Development

1. Clone the repository:
   ```bash
   git clone https://github.com/viable-systems/vsm-docs.git
   cd vsm-docs
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Run the development server:
   ```bash
   mkdocs serve
   ```

4. Open your browser to `http://localhost:8000`

### Building Documentation

To build the static site:

```bash
mkdocs build
```

The built site will be in the `site/` directory.

## Documentation Structure

- **Overview**: Introduction to VSM concepts and architecture
- **Getting Started**: Installation and quick start guides
- **Core Subsystems**: Detailed documentation for S1-S5 subsystems
- **Support Systems**: Algedonic Channel and Temporal Variety Channel
- **Migration Roadmap**: Phased approach to building the VSM system
- **API Reference**: Complete API documentation
- **Guides**: Practical guides for building and integrating VSM systems

## Contributing

Please read our [Contributing Guide](docs/reference/contributing.md) for details on our code of conduct and the process for submitting pull requests.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Links

- [VSM Core Repository](https://github.com/viable-systems/vsm-core)
- [VSM Starter Template](https://github.com/viable-systems/vsm-starter)
- [Official Website](https://viable-systems.github.io)# Trigger deployment after enabling GitHub Pages
