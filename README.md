# Python coding conventions (Forum Virium Helsinki)

This document defines FVH's programming conventions when using Python programming
language. Note that this is work-in-progress project.

If you don't agree with some point, then suggest a change, and we'll discuss it.

# Python versions

We support "security" and "bugfix" versions which are mentioned in the
[Status of Python versions](https://devguide.python.org/versions/#supported-versions)
page, but consider using two latest versions which are currently Python 3.12 and 3.13
(while writing this in February 2025). If your deployment environment doesn't
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
- Automatically run Ruff and other checks before each commit
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

The pre-commit configuration includes Ruff and other essential checks. You can find the
complete configuration in the example [.pre-commit-config.yaml](./.pre-commit-config.yaml).

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
2. Install the SDK: `uv pip install --sentry-sdk`
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

## main, dev, feature, bugfix branches

We follow a branching strategy where `main` branch contains production-ready code, `dev` branch is used for development, and feature/bugfix branches are created for specific changes. Feature branches should be named as `feature/description-of-change` and bugfix branches as `bugfix/issue-description`.

For detailed branching strategy guidelines and best practices, see [Git Flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) documentation.

Always create new branches from `dev` and merge back through pull requests.

TO BE DISCUSSED: should we use `main` branch for production code?

## Code review using pull requests

Pull requests (PRs) are typically mandatory in larger projects unless explicitly agreed otherwise. They serve as a formal mechanism for code review, ensuring that all changes are properly vetted before being merged into the main codebase. This practice helps maintain code quality, catch potential issues early, and keeps the team informed about ongoing changes. Even for smaller changes, creating a PR is recommended as it provides documentation of the change and allows for discussion if needed.

TO BE DISCUSSED: perhaps in personal projects we can use a simpler approach?
