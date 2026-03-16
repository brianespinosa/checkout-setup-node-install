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
- Single update entry with `fix(deps):` conventional commit prefix for all updates
- Auto-rebasing is enabled

### Major version PR title rewriting (`workflows/dependabot-major-prefix.yml`)

- Dependabot cannot natively assign different commit prefixes per semver update type
- A `pull_request_target` workflow detects major-version Dependabot PRs using `dependabot/fetch-metadata`
- Rewrites PR title from `fix(deps): ...` to `feat!(deps): ...` so squash-merge commits trigger major version bumps via git-cliff
- Uses `pull_request_target` (not `pull_request`) because Dependabot PRs have read-only tokens under the `pull_request` event
- Safe because the workflow never checks out PR code — only reads event metadata and edits the PR title

**REQUIRED: All PRs must be merged via squash merge.** The title-rewriting only affects the PR title — the rewritten title becomes the commit message only when squash merging. A regular merge commit preserves the original commit message, causing git-cliff to see `fix(deps):` instead of `feat!(deps):` and produce the wrong version bump.
