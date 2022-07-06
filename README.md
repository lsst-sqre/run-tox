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
- `tox-requirements` (string, optional) Pip requirements for running tox. Default is `tox`, but you can also include tox plugins (e.g., `tox tox-docker`).
- `tox-posargs` (string, optional) Command line arguments to pass to the tox command. The positional arguments are made available as the `{posargs}` substitution to tox environments. Default is an empty string.
- `cache-key-prefix` (string, optional) Prefix for the tox environment cache key. Set to distinguish from other caches. Default is `tox`.

## Outputs

No outputs.


## Developer guide

This repository provides a **composite** GitHub Action, a type of action that packages multiple regular actions into a single step.
We do this to make the GitHub Actions workflows of all our software projects more consistent and easier to maintain.
[You can learn more about composite actions in the GitHub documentation.](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action)

Create new releases using the GitHub Releases UI and assign a tag with a [semantic version](https://semver.org), including a `v` prefix. Choose the semantic version based on compatibility for users of this workflow. If backwards compatibility is broken, bump the major version.

When a release is made, a new major version tag (i.e. `v1`, `v2`) is also made or moved using [nowactions/update-majorver](https://github.com/marketplace/actions/update-major-version).
We generally expect that most users will track these major version tags.
