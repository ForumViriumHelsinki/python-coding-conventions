# Python coding conventions (Forum Virium Helsinki)

This document defines FVH's programming conventions when using Python programming
language. Note that this is work-in-progress project.

If you don't agree with some point, then suggest a change, and we'll discuss it.

# Python versions

We support "security" and "bugfix" versions which are mentioned in the
[Status of Python versions](https://devguide.python.org/versions/#supported-versions)
page, but consider using two latest versions which are currently Python 3.12 and 3.13
(while writing this in August 2025). If your deployment environment doesn't
support these, consider installing the latest Python or using Docker containers,
where you should always use the latest version of Python.

## Virtual environments

Use `uv venv` to create virtual environments. UV is a fast Python package installer
and resolver written in Rust. It offers several advantages over traditional tools:

- Up to 10-100x faster than pip for installations
- Built-in dependency resolution that's more reliable than pip
- Native support for modern package formats and lockfiles
- Lower memory usage and better system resource utilization
- Automatic virtual environment management

```bash
# Install uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# Or on macOS
brew install uv

# Create new virtual environment
uv venv venv  # optional: --python 3.13

# Activate virtual environment
source venv/bin/activate
```

UV replaces both venv and pip functionality with improved performance. Do not use
virtualenv, pyenv, or other alternatives when creating virtual environments in scripts.

## Install dependencies

Install default dependencies and development dependencies in active virtual environment in one go:

```bash
uv sync --active --extra dev
```

# Mandatory tools to use

If you are the one and only person who is writing and viewing the code in your
project, it is highly recommended to use tools listed below. You can safely try
all commands to see what they would do.

Many IDEs (PyCharm, VSCode, Cursor, etc.) have plugins for Ruff, so you can use it without additional setup.

## Required Development Tools

### Ruff

[Ruff](https://docs.astral.sh/ruff/) is an extremely fast Python linter and code formatter,
written in Rust. It replaces multiple tools (like black, isort, flake8, etc.) with a single,
unified solution.

Check and fix code style:

`ruff check --fix .`

Format code:

`ruff format .`

Configuration is already set up in the project's [pyproject.toml](./pyproject.toml) file.
See the configuration for detailed rules and settings.

### Pre-commit

Pre-commit hooks are mandatory for all repositories under Forum Virium Helsinki. These hooks:
- Automatically run Ruff, mypy, and other checks before each commit
- Ensure code quality standards are met
- Prevent commits that don't meet the required standards
- Save time by catching issues early in the development process

To set up pre-commit:

1. Copy [.pre-commit-config.yaml](./.pre-commit-config.yaml) to your repository root
2. Install and activate pre-commit:
```bash
uv pip install pre-commit && pre-commit install
```

If you have already run `uv sync --active --extra dev`, you can skip the installation step. You can update the pre-commit hooks by running: `pre-commit autoupdate`.

The pre-commit configuration includes Ruff, mypy type checking, and other essential checks.
You can find the complete configuration in the example [.pre-commit-config.yaml](./.pre-commit-config.yaml).

### Type Checking with mypy

[mypy](https://mypy.readthedocs.io/) is a static type checker for Python that helps catch type-related errors before runtime. Type checking is mandatory for all new projects.

Key benefits:
- Catches type errors at development time
- Improves code documentation through type annotations
- Makes refactoring safer and easier
- Enhances IDE support with better autocomplete

Run type checking manually:

`mypy src/`

Configuration is set up in [pyproject.toml](./pyproject.toml) with strict settings enabled.
mypy is also automatically run as part of pre-commit hooks.

# Other recommended tools

## Sentry

We use [sentry.io](https://sentry.io) to track errors in our production
deployments. Sentry provides real-time error tracking and monitoring:

- Automatic error capturing and reporting
- Detailed stack traces and context
- Performance monitoring
- Release tracking and source maps

To set up Sentry in your project:

1. Ask sysadmins for your project's Sentry DSN
2. Install the SDK: `uv add sentry-sdk`
3. Initialize Sentry in your application:

```python
import sentry_sdk

sentry_sdk.init(
    dsn="your-dsn-here",
    traces_sample_rate=1.0,
    environment="production"
)
```

## Code review by asking someone

Code review by a colleague is recommended, especially when you're new to the project or
working on complex features. While not always mandatory for minor changes, it helps to:

- Catch potential issues early
- Ensure code follows project standards
- Share knowledge within the team

When in doubt, always ask for a review. Your colleagues are there to help!

## Branching strategy

We use a simplified branching strategy focused on the `main` branch:

- **Main branch** is used for active development
- **Feature/bugfix branches** are created from `main` for specific changes
- Branch naming: `feature/description-of-change` and `bugfix/issue-description`
- Aim for **short-lived branches** and merge often to avoid conflicts
- When working alone on initial development, you can commit directly to `main`

### Merge strategy

- Use **rebase** or **squash** when merging pull requests
- Avoid merge commits to keep commit history linear and clean
- This makes it easier to track changes and debug issues later

## Code review using pull requests

Pull requests (PRs) are recommended for code review, but the approach can be flexible based on project size and team:

**For larger projects or team collaborations:**
- PRs are mandatory for all changes
- Formal code review process ensures quality and knowledge sharing
- All changes must be approved before merging

**For smaller projects or personal work:**
- You can commit directly to `main` during initial development phases
- Use PRs when you want feedback or for significant changes
- Consider creating PRs even for solo work to document important changes

**Benefits of using PRs:**
- Maintains code quality through peer review
- Catches potential issues early in development
- Provides documentation of changes and reasoning
- Keeps the team informed about ongoing work

## Release management and automation

### Releases from main branch

Releases are published directly from stable points in the `main` branch:

- No separate release branches needed
- It's acceptable for `main` to be temporarily broken during development
- Release when you reach a stable, tested state
- Tag releases with semantic versioning (e.g., `v1.2.3`)

### Automated release process

The release process can be fully automated using CI/CD tools. This automation works best with:

**Conventional Commits:**
- Use standardized commit message format
- Examples:
  - `feat: add user authentication system`
  - `fix: resolve database connection timeout`
  - `docs: update installation instructions`
  - `chore: update dependencies`

**Automated benefits:**
- Automatic version number bumping based on commit types
- Generated release notes from commit messages
- Automated changelog updates
- Consistent release process across projects

**Commit types:**
- `feat`: New features (minor version bump)
- `fix`: Bug fixes (patch version bump)
- `BREAKING CHANGE`: Breaking changes (major version bump)
- `docs`, `style`, `refactor`, `test`, `chore`: No version bump

This approach reduces manual work and ensures consistent, documented releases.
