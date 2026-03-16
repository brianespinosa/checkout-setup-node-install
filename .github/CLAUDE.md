# .github/ — Claude Code Guidance

## Overview

This directory contains GitHub Actions workflows and Dependabot configuration for the checkout-setup-node-install composite action.

## Release Workflow (`workflows/release.yml`)

- Triggers on push to `main`
- Uses `git-cliff` to calculate the next semver tag from conventional commits
- Creates a GitHub Release with generated changelog as the body — no `CHANGELOG.md` file is committed
- Configuration lives in `cliff.toml` at the repo root
- Skips release creation when no version-bump-worthy commits exist since the last tag

**Semver mapping** (handled by git-cliff via conventional commits):
- `feat` → minor bump
- `fix` → patch bump
- `!` suffix or `BREAKING CHANGE` footer → major bump
- `chore`, `ci`, `docs`, `style` → no bump

## Dependabot (`dependabot.yml`)

- Targets `github-actions` ecosystem only
- Uses **two separate update entries** to produce different conventional commit prefixes based on update type:
  - Minor/patch updates use `fix(deps):` prefix → triggers patch version bump
  - Major updates use `feat!(deps):` prefix → triggers major version bump
- This workaround exists because Dependabot does not natively support different prefixes per semver bump type
- Auto-rebasing is enabled on both entries
