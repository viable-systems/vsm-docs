# Phase 1: Foundation Setup (Week 1)

The foundation phase establishes the organizational infrastructure and core templates that all subsequent packages will build upon.

## Objectives

1. Create GitHub organization and configure settings
2. Establish VSM starter template with best practices
3. Set up documentation infrastructure
4. Configure external services and integrations

## Deliverables

### 1.1 GitHub Organization Setup

**Create @viable-systems organization**

```bash
# Organization settings to configure:
- Organization name: viable-systems
- Display name: Viable Systems
- Description: Open-source Viable Systems Model implementation in Elixir
- Email: contact@viable-systems.org
- URL: https://viable-systems.github.io
```

**Teams Structure:**
- **Core**: Repository admin access
- **Contributors**: Write access to all repos
- **Documentation**: Write access to docs repos
- **Community**: Triage and discussion moderation

**Organization Settings:**
- Enable 2FA requirement
- Configure security policies
- Set up CODEOWNERS templates
- Enable dependency scanning
- Configure secret scanning

### 1.2 VSM Starter Template ⭐

**Repository: `viable-systems/vsm-starter`**

This critical template provides the foundation for all VSM packages:

```elixir
# mix.exs template
defmodule VsmPackageName.MixProject do
  use Mix.Project

  @version "0.1.0"
  @source_url "https://github.com/viable-systems/vsm-package-name"

  def project do
    [
      app: :vsm_package_name,
      version: @version,
      elixir: "~> 1.15",
      start_permanent: Mix.env() == :prod,
      deps: deps(),
      docs: docs(),
      package: package(),
      dialyzer: dialyzer(),
      test_coverage: [tool: ExCoveralls],
      preferred_cli_env: [
        coveralls: :test,
        "coveralls.detail": :test,
        "coveralls.post": :test,
        "coveralls.html": :test
      ]
    ]
  end

  def application do
    [
      extra_applications: [:logger]
    ]
  end

  defp deps do
    [
      # Dev/Test dependencies
      {:ex_doc, "~> 0.30", only: :dev, runtime: false},
      {:credo, "~> 1.7", only: [:dev, :test], runtime: false},
      {:dialyxir, "~> 1.4", only: [:dev], runtime: false},
      {:excoveralls, "~> 0.18", only: :test}
    ]
  end

  defp docs do
    [
      main: "readme",
      source_url: @source_url,
      source_ref: "v#{@version}",
      extras: ["README.md", "CHANGELOG.md", "LICENSE"]
    ]
  end

  defp package do
    [
      description: "Package description",
      licenses: ["MIT"],
      links: %{
        "GitHub" => @source_url,
        "VSM Docs" => "https://viable-systems.github.io"
      },
      maintainers: ["Viable Systems Team"]
    ]
  end

  defp dialyzer do
    [
      plt_file: {:no_warn, "priv/plts/dialyzer.plt"},
      flags: [:error_handling, :unknown]
    ]
  end
end
```

**GitHub Actions Workflow:**

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    name: Test (Elixir {% raw %}${{ matrix.elixir }}{% endraw %} / OTP {% raw %}${{ matrix.otp }}{% endraw %})
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        elixir: ['1.15', '1.16']
        otp: ['25.3', '26.0']
    
    steps:
      - uses: actions/checkout@v4
      
      - uses: erlef/setup-beam@v1
        with:
          elixir-version: {% raw %}${{ matrix.elixir }}{% endraw %}
          otp-version: {% raw %}${{ matrix.otp }}{% endraw %}
      
      - name: Restore dependencies cache
        uses: actions/cache@v3
        with:
          path: deps
          key: {% raw %}${{ runner.os }}-mix-${{ hashFiles('**/mix.lock') }}{% endraw %}
          restore-keys: {% raw %}${{ runner.os }}-mix-{% endraw %}
      
      - name: Install dependencies
        run: mix deps.get
      
      - name: Check formatting
        run: mix format --check-formatted
      
      - name: Run credo
        run: mix credo --strict
      
      - name: Run tests
        run: mix test
      
      - name: Run dialyzer
        run: mix dialyzer

  publish:
    name: Publish to Hex
    runs-on: ubuntu-latest
    needs: test
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    
    steps:
      - uses: actions/checkout@v4
      
      - uses: erlef/setup-beam@v1
        with:
          elixir-version: '1.15'
          otp-version: '25.3'
      
      - name: Publish
        run: |
          mix hex.config api_key {% raw %}${{ secrets.HEX_API_KEY }}{% endraw %}
          mix hex.publish --yes
```

**Standard Files:**

1. **README.md Template:**
```markdown
# VSM Package Name

[![Hex.pm](https://img.shields.io/hexpm/v/vsm_package_name.svg)](https://hex.pm/packages/vsm_package_name)
[![Docs](https://img.shields.io/badge/hex-docs-blue.svg)](https://hexdocs.pm/vsm_package_name)
[![CI](https://github.com/viable-systems/vsm-package-name/actions/workflows/ci.yml/badge.svg)](https://github.com/viable-systems/vsm-package-name/actions/workflows/ci.yml)

Package description goes here.

## Installation

Add `vsm_package_name` to your list of dependencies in `mix.exs`:

\`\`\`elixir
def deps do
  [
    {:vsm_package_name, "~> 0.1.0"}
  ]
end
\`\`\`

## Usage

[Usage examples]

## Documentation

Full documentation can be found at [https://hexdocs.pm/vsm_package_name](https://hexdocs.pm/vsm_package_name).

## Contributing

See the [contribution guidelines](https://github.com/viable-systems/vsm-docs/blob/main/CONTRIBUTING.md).

## License

MIT License. See [LICENSE](LICENSE) for details.
```

2. **.formatter.exs:**
```elixir
[
  import_deps: [:phoenix],
  plugins: [Phoenix.LiveView.HTMLFormatter],
  inputs: [
    "{mix,.formatter}.exs",
    "{config,lib,test}/**/*.{ex,exs}",
    "priv/*/seeds.exs"
  ],
  line_length: 98
]
```

### 1.3 Documentation Infrastructure 📚

**Repository: `viable-systems/vsm-docs`**

Central documentation hub (this repository) with:
- MkDocs Material theme
- Auto-deployment to GitHub Pages
- Comprehensive navigation
- Search functionality
- API documentation generation

### 1.4 External Services Setup

**Hex.pm Organization:**
```bash
# Create organization: viable_systems
# Add team members
# Configure permissions
```

**Docker Hub:**
```bash
# Namespace: viablesystems
# Create repositories for each deployable application
# Set up automated builds
```

**CI/CD Secrets:**
Create organization secrets:
- `HEX_API_KEY`: For publishing packages
- `DOCKER_PASSWORD`: For container registry
- `DISCORD_WEBHOOK`: For notifications
- `CODECOV_TOKEN`: For coverage reports

## Validation Checklist

### Organization Setup
- [ ] GitHub organization created
- [ ] Teams configured with proper permissions
- [ ] Security settings enabled
- [ ] Branch protection rules template created

### VSM Starter
- [ ] Repository created and configured
- [ ] All template files in place
- [ ] CI/CD workflow tested
- [ ] Example package generated successfully

### Documentation
- [ ] MkDocs site building successfully
- [ ] GitHub Pages deployment working
- [ ] Search functionality operational
- [ ] All navigation links valid

### External Services
- [ ] Hex.pm organization active
- [ ] Docker Hub namespace created
- [ ] All CI/CD secrets configured
- [ ] Integration tests passing

## Migration Notes

### For Existing Code
When migrating code from the monolith:
1. Use `mix new` with the starter template
2. Copy source files to new structure
3. Update module names to new namespace
4. Add package-specific dependencies
5. Write package-specific tests

### Naming Conventions
- GitHub repos: `vsm-package-name` (kebab-case)
- Hex packages: `vsm_package_name` (snake_case)
- Module names: `VSM.PackageName` (PascalCase)
- App names: `:vsm_package_name` (snake_case atom)

## Common Issues

### Problem: Hex Publishing Fails
**Solution:** Ensure HEX_API_KEY is set correctly and user has organization permissions

### Problem: Docker Builds Timeout
**Solution:** Use multi-stage builds and optimize layer caching

### Problem: Documentation Not Updating
**Solution:** Check GitHub Pages settings and deployment action logs

## Next Steps

With the foundation in place, we can now begin extracting core dependencies in [Phase 2](phase-2.md).

Key repositories to create immediately:
1. `vsm-starter` (this phase)
2. `vsm-telemetry` (next phase)
3. `vsm-goldrush` (next phase)

---

*Next: [Phase 2 - Core Dependencies](phase-2.md) →*