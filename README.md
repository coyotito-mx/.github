<p align="center">
  <img src="resources/hero.png" alt="COYOTITO's Public Actions" width="720">
</p>

# COYOTITO — Public Actions

Reusable GitHub Actions workflows for PHP / Laravel projects: tests, code style, static analysis, asset compilation, release drafting and changelog updates.

## Versioning

Pin to the **major** tag to receive backwards-compatible patches and new features automatically:

```yaml
uses: asciito/github/.github/workflows/run-tests.yml@v1
```

Pin to an **exact** version for immutable, reproducible builds:

```yaml
uses: asciito/github/.github/workflows/run-tests.yml@v1.0.0
```

Major bumps (`v2`, `v3`, ...) signal breaking changes. Consumers on `@v1` stay on the latest `v1.x.y` until they choose to migrate.

## Workflows

| Workflow | Purpose | Notable inputs |
|---|---|---|
| `run-tests.yml` | Run Pest (default) or PHPUnit. Supports a PHP matrix. | `php` (JSON array), `extensions`, `tools`, `dependency-version`, `pest`, `parallel` |
| `coding-standards.yml` | Run Laravel Pint. Optional auto-commit of the fix. | `php`, `fix`, `preset`, `message`, `extensions`, `tools` |
| `static-analisys.yml` | Run PHPStan. | `php`, `extensions`, `tools`, `dependency-version`, `memory_limit` |
| `compile-assets.yml` | Run `npm ci` + `npm run <cmd>`. Optional auto-commit of the build. | `node`, `cmd`, `build_path`, `working-directory`, `autocommit`, `commit_message` |
| `release-drafter.yml` | Draft a GitHub release. Requires `secrets.PAT`. | `config-name`, `publish`, `prerelease` |
| `update-changelog.yml` | Update `CHANGELOG.md` and open a PR after a release. | `branch`, `version`, `notes`, `pr-title`, `pr-labels`, `commit-message` |

## Example

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  tests:
    uses: asciito/github/.github/workflows/run-tests.yml@v1
    with:
      php: '["8.3", "8.4"]'
      dependency-version: stable

  lint:
    uses: asciito/github/.github/workflows/coding-standards.yml@v1
    with:
      fix: false
```

## How releases work in this repo

- Every push to `main` updates a release draft via `release-drafter`.
- The draft picks its next version (major / minor / patch) from PR labels (`breaking`, `feature`, `fix`, ...).
- When the draft is published, the `v<MAJOR>` tag is automatically moved to the new release, so external consumers on `@v1` get the update without any manual tagging.
