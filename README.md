# Python coding conventions (Forum Virium Helsinki)

This document defines FVH's programming conventions when using Python programming
language. Note that this is work-in-progress project. 
If you don't agree with some point, then suggest a change, and we'll discuss it.

Some coding conventions below are derived from here:
https://dev.hel.fi/coding-standards

# Python versions

We support versions which are mentioned in the [Status of Python 
versions](https://devguide.python.org/versions/#supported-versions) page,
but consider using two latest versions which are currently Python 3.10 and 3.11
(while writing this in October 2022). If your deployment environment doesn't
support these, consider installing the latest Python (using pyenv) or using 
Docker containers, where you should always use the latest version of Python.

## Virtual environments

Use `/path/to/latest/python -m venv /path/to/venv` for creating 
virtualenvs or (`pyenv virtualenv` if you prefer pyenv), not virtualenv or other 
alternatives when using virtual environments in scripts.

# Mandatory tools to use

If you are the one and only person who is writing and viewing the code in your
project, it is highly recommended to use tools listed below. You can safely try
all commands to see what they would do

## black

Newer projects use [black](https://black.readthedocs.io/) for Python code
formatting.  It is a PEP 8 compliant opinionated formatter.
We follow the basic config, without any modifications but line
length which may be up to 120 characters. Black should be required for new
projects and is warmly welcome for the old ones.

Check what would be changed:

`black --diff --line-length 120 file.py`

Fix file(s):

`black --line-length 120 file.py`

Set up configuration in pyproject.toml file.

## isort

[isort](https://pycqa.github.io/isort/) sorts imports alphabetically, 
and automatically separates them into sections and by type.
To make it compatible with black add the following line either 
to your project's pyproject.toml file's tool.isort section.

```
[tool.isort]
line_length = 120
multi_line_output = 3
include_trailing_comma = true
force_grid_wrap = 0
use_parentheses = true
ensure_newline_before_comments = true
skip = ["migrations", "venv"]
```

Check what would be changed:  

`isort --diff file.py`

Fix file(s):  

`isort file.py`

## autoflake

[autoflake](https://github.com/PyCQA/autoflake#introduction) removes unused 
imports and unused variables from Python code.

Check what would be changed:

`autoflake --remove-all-unused-imports --remove-unused-variables file.py`

Fix file(s):

`autoflake --in-place --remove-all-unused-imports --remove-unused-variables file.py`

## flake8

Flake8 is a Python library that wraps PyFlakes, pycodestyle and Ned Batchelder's McCabe 
script to check the style and quality of some python code.

Check style and formatting errors:

`flake8 --max-line-length 120 file.py`

Set up configuration in [.flake8](./.flake8) file.

## Configuration files

Unfortunately all tools don't support pyproject.toml yet. It should be used when 
possible, but e.g. flake8 needs its .flake8 file.

### requirements-dev.txt

Install modules defined in [requirements-dev.txt](./requirements-dev.txt) into 
your development environment's virtualenv using command:

`pip install -U -r requirements-dev.txt`

### .flake8

Currently, flake8 is not compatible with pyproject.toml so put its config in 
a dedicated [.flake8](./.flake8) file.
It may contain just `max-line-length = 120` or smaller value for that.

### pyproject.toml

[pyproject.toml](./pyproject.toml) contains configuration for 
black, isort and autoflake.

### pre-commit + githooks

If you are pushing code to a repository under 
[Forum Virium's Github organization](https://github.com/ForumViriumHelsinki/)
you must use [pre-commit](https://pre-commit.com/) and Git hooks.

You can find example [.pre-commit-config.yaml](./.pre-commit-config.yaml)
configuration file in this repository.

#### TL;DR

Copy [.pre-commit-config.yaml](./.pre-commit-config.yaml) 
to the root of your repository, then:

`pip install pre-commit && pre-commit install`

# Other recommended tools

## Sentry

We use [sentry.io](https://sentry.io) to track errors in our production 
deployments. Ask sysadmins for Sentry DSN.

# TODO

## Code review by asking someone

## main, dev, feature, bugfix branches

## code review using pull requests




