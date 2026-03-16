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
- All dependency updates use `fix(deps):` commit prefix → triggers patch version bump
- Auto-rebasing is enabled
- For major dependency updates that are breaking, manually create a `feat!:` commit when needed
