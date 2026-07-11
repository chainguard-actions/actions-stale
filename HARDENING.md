<!-- markdownlint-disable -->

# Hardening Report: actions--stale/v8.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **actions--stale/v8.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions/reusable workflows using mutable branch refs (@main) or version tags (@v3, @v0.2.2) instead of pinned 40-character SHA commit hashes. Affected references: basic-validation.yml: `actions/reusable-workflows/...@main`; check-dist.yml: `actions/reusable-workflows/...@main`; codeql-analysis.yml: `actions/reusable-workflows/...@main`; e2e-tests.yml: `actions/checkout@v3`; licensed.yml: `actions/reusable-workflows/...@main`; release-new-action-version.yml: `actions/publish-action@v0.2.2`; update-config-files.yml: `actions/reusable-workflows/...@main`.

Locations:

- `.github/workflows/basic-validation.yml:14`
- `.github/workflows/check-dist.yml:14`
- `.github/workflows/codeql-analysis.yml:13`
- `.github/workflows/e2e-tests.yml:17`
- `.github/workflows/licensed.yml:13`
- `.github/workflows/release-new-action-version.yml:22`
- `.github/workflows/update-config-files.yml:13`

### missing-permissions (severity: medium)

These workflow files have no top-level `permissions:` key and no per-job `permissions:` key, meaning they run with the default (potentially write) token permissions. Each should declare minimal required permissions.

Locations:

- `.github/workflows/basic-validation.yml:1`
- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/e2e-tests.yml:1`
- `.github/workflows/licensed.yml:1`
- `.github/workflows/update-config-files.yml:1`

### script-injection (severity: high)

Sub-rule (a): A `run:` block in e2e-tests.yml directly interpolates a `${{ ... }}` expression — specifically `${{ format('{0},{1}', toJSON(steps.stale.outputs.staled-issues-prs), toJSON(steps.stale.outputs.closed-issues-prs)) }}` — into a shell command. Any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted into the shell command string before the shell parses it. The value should be passed via an `env:` variable and referenced as a quoted shell variable instead.

Locations:

- `.github/workflows/e2e-tests.yml:26`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 7 workflow files with unpinned action references by resolving them to full 40-character SHA commit hashes (with tag comments for readability). Added `permissions: {}` top-level blocks to the 6 workflow files missing permissions declarations. Fixed the script injection in e2e-tests.yml by moving the `${{ format(...) }}` expression into an `env:` variable (`STALE_OUTPUTS`) and referencing it as a quoted shell variable (`"$STALE_OUTPUTS"`) in the `run:` block.

