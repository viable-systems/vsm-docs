# Contributing to VSM Documentation

We love your input! We want to make contributing to VSM documentation as easy and transparent as possible, whether it's:

- Reporting a typo or error
- Discussing the current state of the documentation
- Submitting a fix
- Proposing new sections or guides
- Becoming a maintainer

## We Develop with GitHub

We use GitHub to host documentation, to track issues and feature requests, as well as accept pull requests.

## We Use [GitHub Flow](https://guides.github.com/introduction/flow/index.html)

Pull requests are the best way to propose changes to the documentation:

1. Fork the repo and create your branch from `main`.
2. If you've added content, ensure it follows our style guide.
3. If you've changed navigation, update the `mkdocs.yml` file.
4. Ensure the documentation builds without errors.
5. Make sure your prose is clear and grammatically correct.
6. Issue that pull request!

## Any contributions you make will be under the MIT Software License

In short, when you submit documentation changes, your submissions are understood to be under the same [MIT License](LICENSE) that covers the project.

## Report issues using GitHub's [issue tracker](https://github.com/viable-systems/vsm-docs/issues)

We use GitHub issues to track public bugs and requests. Report an issue by [opening a new issue](https://github.com/viable-systems/vsm-docs/issues/new).

## Write bug reports with detail, background, and sample code

**Great Bug Reports** tend to have:

- A quick summary and/or background
- What you expected would happen
- What actually happens
- Notes (possibly including why you think this might be happening, or stuff you tried that didn't work)

## Documentation Style Guide

### General Principles

- **Clarity First**: Write for developers who may be new to VSM concepts
- **Examples**: Include code examples wherever possible
- **Consistency**: Follow established patterns in existing documentation
- **Accuracy**: Ensure technical details are correct

### Markdown Formatting

- Use ATX-style headers (`#`, `##`, etc.)
- Prefer fenced code blocks with language specifiers
- Use tables for comparing options or listing parameters
- Include diagrams using Mermaid when helpful

### Code Examples

```elixir
# Good: Clear, runnable example
defmodule MySystem.S1.Worker do
  use VSM.S1.Operation
  
  def handle_task(task, state) do
    # Process the task
    {:ok, result}
  end
end
```

### File Structure

- Place new guides in appropriate directories
- Use kebab-case for file names: `my-new-guide.md`
- Update navigation in `mkdocs.yml` when adding new pages

### Writing Tone

- Professional but approachable
- Avoid jargon without explanation
- Use active voice
- Be concise but complete

## Building Documentation Locally

1. Install Python dependencies:
   ```bash
   pip install -r requirements.txt
   ```

2. Run the development server:
   ```bash
   mkdocs serve
   ```

3. View at `http://localhost:8000`

4. Build static site:
   ```bash
   mkdocs build
   ```

## License

By contributing, you agree that your contributions will be licensed under its MIT License.

## References

This document was adapted from the open-source contribution guidelines for [Facebook's Draft](https://github.com/facebook/draft-js/blob/master/CONTRIBUTING.md).