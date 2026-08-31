<!-- markdownlint-disable -->

# Hardening Report: actions--stale/v11.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **actions--stale/v11.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is interpolated directly inside a run: shell command. The expression `${{ format('{0},{1}', toJSON(steps.stale.outputs.staled-issues-prs), toJSON(steps.stale.outputs.closed-issues-prs)) }}` is expanded by the GitHub Actions template engine before the shell sees it, allowing an attacker who controls the action's outputs to inject arbitrary shell commands. The offending line is: `run: echo ${{ format('{0},{1}', toJSON(steps.stale.outputs.staled-issues-prs), toJSON(steps.stale.outputs.closed-issues-prs)) }}`

Locations:

- `.github/workflows/e2e-tests.yml:22`

### unpinned-uses (severity: high)

Multiple workflow files reference external actions and reusable workflows using mutable tags or branch names instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks. Failing references: basic-validation.yml: `actions/reusable-workflows/...@main`; check-dist.yml: `actions/reusable-workflows/...@main`; codeql-analysis.yml: `actions/reusable-workflows/...@main`; e2e-tests.yml: `actions/checkout@v6`; licensed.yml: `actions/reusable-workflows/...@main`; publish-immutable-actions.yml: `actions/checkout@v6`, `actions/publish-immutable-action@v0.0.4`; release-new-action-version.yml: `actions/publish-action@v0.4.0`; update-config-files.yml: `actions/reusable-workflows/...@main`.

Locations:

- `.github/workflows/basic-validation.yml:13`
- `.github/workflows/check-dist.yml:14`
- `.github/workflows/codeql-analysis.yml:13`
- `.github/workflows/e2e-tests.yml:15`
- `.github/workflows/licensed.yml:13`
- `.github/workflows/publish-immutable-actions.yml:16`
- `.github/workflows/publish-immutable-actions.yml:18`
- `.github/workflows/release-new-action-version.yml:21`
- `.github/workflows/update-config-files.yml:12`

### missing-permissions (severity: medium)

Six workflow files have no top-level `permissions:` block and no job-level `permissions:` block on any of their jobs. Without explicit permissions, workflows inherit the default repository token permissions (which may be broad), violating the principle of least privilege. Affected files: basic-validation.yml, check-dist.yml, codeql-analysis.yml, e2e-tests.yml, licensed.yml, update-config-files.yml.

Locations:

- `.github/workflows/basic-validation.yml:1`
- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/e2e-tests.yml:1`
- `.github/workflows/licensed.yml:1`
- `.github/workflows/update-config-files.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across 8 workflow files:

1. script-injection (e2e-tests.yml): Moved `${{ format('{0},{1}', toJSON(steps.stale.outputs.staled-issues-prs), toJSON(steps.stale.outputs.closed-issues-prs)) }}` from the `run:` shell command into the step's `env:` block as `STALE_OUTPUTS`, then referenced it safely as `"$STALE_OUTPUTS"` in the shell.

2. unpinned-uses: Pinned all mutable references to full 40-char SHAs:
   - actions/reusable-workflows@main → @d468c63c53c1184242904d1a3ac74fd1081f36c8 (5 files)
   - actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 (2 files)
   - actions/publish-immutable-action@v0.0.4 → @4bc8754ffc40f27910afb20287dbbbb675a4e978
   - actions/publish-action@v0.4.0 → @23f4c6f12633a2da8f44938b71fde9afec138fb4

3. missing-permissions: Added `permissions: {}` top-level block to basic-validation.yml, check-dist.yml, codeql-analysis.yml, e2e-tests.yml, licensed.yml, and update-config-files.yml.

