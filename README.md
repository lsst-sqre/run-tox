# run-tox

This is a [composite GitHub Action](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action) for running CI tasks through [tox](https://tox.wiki/en/latest/index.html#).

The action:

1. Sets up Python
2. Installs/updates pip along with tox and any other tox plugins such as tox-docker
3. Caches the tox environment
4. Runs tox with the environments you specify

## Example usage

```yaml
name: Python CI

"on":
  push:
    tags:
      - "*"
    branches:
      - "main"
  pull_request: {}

jobs:

  tests:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python:
          - "3.8"
          - "3.9"
          - "3.10"

    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0 # full history for setuptools_scm

      - name: Run tox
        uses: lsst-sqre/run-tox@v1
        with:
          python-version: ${{ matrix.python }}
          tox-envs: "typing,py"
```

## Inputs

- `python-version` (string, required) the Python version.
- `tox-envs` (string, required) the tox environments to run, as a comma-delimited list. Example: `typing,py` to run a type checking environment and the Python environment.
- `tox-package` (string, optional) Pip requirement for tox itself (argument to `pip install`). Default is `tox`, without any version constraints.
- `tox-plugins` (string, optional) Pip requirements for any tox plugins (arguments to `pip install`). Default is an empty string, but can be set to `tox-docker` to install Docker support, for example.
- `tox-posargs` (string, optional) Command line arguments to pass to the tox command. The positional arguments are made available as the `{posargs}` substitution to tox environments. Default is an empty string.
- `cache-key-prefix` (string, optional) Prefix for the tox environment cache key. Set to distinguish from other caches. Default is `tox`.
- `use-cache` (boolean, optional) Flag is enable caching of the tox environment. Default is `true`.

## Outputs

No outputs.

## Usage tips

### Docker support for tox

[tox-docker](https://github.com/tox-dev/tox-docker) is a plugin that lets you run Docker containers during your tox environment runs.
This is a great way for your tests to use services like Postgres or Redis.
To make `tox-docker` available, specify it in the `tox-plugins` argument:

```yaml
name: Python CI

"on":
  push:
    tags:
      - "*"
    branches:
      - "main"
  pull_request: {}

jobs:

  tests:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python:
          - "3.8"
          - "3.9"
          - "3.10"

    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0 # full history for setuptools_scm

      - name: Run tox
        uses: lsst-sqre/run-tox@v1
        with:
          python-version: ${{ matrix.python }}
          tox-envs: "typing,py"
          tox-plugins: "tox-docker"
```

## Developer guide

This repository provides a **composite** GitHub Action, a type of action that packages multiple regular actions into a single step.
We do this to make the GitHub Actions workflows of all our software projects more consistent and easier to maintain.
[You can learn more about composite actions in the GitHub documentation.](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action)

Create new releases using the GitHub Releases UI and assign a tag with a [semantic version](https://semver.org), including a `v` prefix. Choose the semantic version based on compatibility for users of this workflow. If backwards compatibility is broken, bump the major version.

When a release is made, a new major version tag (i.e. `v1`, `v2`) is also made or moved using [nowactions/update-majorver](https://github.com/marketplace/actions/update-major-version).
We generally expect that most users will track these major version tags.
