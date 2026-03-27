# python-actions

Reusable GitHub Actions workflows for Python projects using [uv](https://docs.astral.sh/uv/).

## Available Workflows

### `python-quality.yml` - Lint & Pre-commit

Runs **pre-commit** hooks (including ruff lint and format checks) against your codebase.

**Requirements for consumer repos:**
- A `pyproject.toml` with uv-compatible project config
- A `uv.lock` lockfile (run `uv sync` locally)
- A `.pre-commit-config.yaml` (with ruff hooks for lint/format)
- `pre-commit` in your dev dependencies

#### Inputs

| Input               | Required | Default       | Description                            |
| ------------------- | -------- | ------------- | -------------------------------------- |
| `python-version`    | No       | `3.12`        | Python version to use                  |
| `working-directory` | No       | `.`           | Working directory for all steps        |
| `pre-commit-args`   | No       | `--all-files` | Additional arguments for `pre-commit`  |

#### Usage

Create a workflow in your repo at `.github/workflows/quality.yml`:

```yaml
name: Quality

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  quality:
    uses: dpatino00/python-actions/.github/workflows/python-quality.yml@v1
    with:
      python-version: "3.12"
```

#### With custom options

```yaml
jobs:
  quality:
    uses: dpatino00/python-actions/.github/workflows/python-quality.yml@v1
    with:
      python-version: "3.11"
      working-directory: "backend"
```

### `python-tests.yml` - Tests

Runs **pytest** against your codebase with a **matrix** of Python versions.

**Requirements for consumer repos:**
- A `pyproject.toml` with uv-compatible project config
- A `uv.lock` lockfile (run `uv sync` locally)
- `pytest` in your dev dependencies
- A `tests/` directory (or pytest-discoverable test files)

#### Inputs

| Input               | Required | Default                          | Description                              |
| ------------------- | -------- | -------------------------------- | ---------------------------------------- |
| `python-versions`   | No       | `["3.11","3.12","3.13","3.14"]`  | JSON array of Python versions to test    |
| `working-directory` | No       | `.`                              | Working directory for all steps          |
| `pytest-args`       | No       | `""`                             | Additional arguments for pytest          |

#### Usage

Uses the default matrix (3.11, 3.12, 3.13, 3.14):

```yaml
name: Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  tests:
    uses: dpatino00/python-actions/.github/workflows/python-tests.yml@v1
```

#### With custom versions

```yaml
jobs:
  tests:
    uses: dpatino00/python-actions/.github/workflows/python-tests.yml@v1
    with:
      python-versions: '["3.12","3.13"]'
      pytest-args: "-v --tb=short"
      working-directory: "backend"
```

---

## Adding to Your Project

1. Ensure your project uses `uv` with dev dependencies:

   ```bash
   uv add --dev ruff pre-commit pytest
   uv sync --dev
   ```

2. Add a `.pre-commit-config.yaml` if you don't have one:

   ```yaml
   repos:
     - repo: https://github.com/astral-sh/ruff-pre-commit
       rev: v0.9.10
       hooks:
         - id: ruff
           args: [--fix]
         - id: ruff-format

     - repo: https://github.com/pre-commit/pre-commit-hooks
       rev: v5.0.0
       hooks:
         - id: trailing-whitespace
         - id: end-of-file-fixer
         - id: check-yaml
         - id: check-added-large-files
   ```

3. Add the caller workflow (see Usage above).

## Versioning

This repo uses tags for stable references:

- `@v1` - Latest v1.x (recommended)
- `@main` - Bleeding edge (not recommended for production)
