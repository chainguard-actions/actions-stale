<!-- markdownlint-disable -->

# Hardening Report: actions--stale/v10.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **actions--stale/v10.4.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks. Failing references: basic-validation.yml uses actions/reusable-workflows/...@main; check-dist.yml uses actions/reusable-workflows/...@main; codeql-analysis.yml uses actions/reusable-workflows/...@main; e2e-tests.yml uses actions/checkout@v6; licensed.yml uses actions/reusable-workflows/...@main; publish-immutable-actions.yml uses actions/checkout@v6 and actions/publish-immutable-action@v0.0.4; release-new-action-version.yml uses actions/publish-action@v0.4.0; update-config-files.yml uses actions/reusable-workflows/...@main.

Locations:

- `.github/workflows/basic-validation.yml:14`
- `.github/workflows/check-dist.yml:14`
- `.github/workflows/codeql-analysis.yml:13`
- `.github/workflows/e2e-tests.yml:17`
- `.github/workflows/licensed.yml:13`
- `.github/workflows/publish-immutable-actions.yml:14`
- `.github/workflows/publish-immutable-actions.yml:16`
- `.github/workflows/release-new-action-version.yml:22`
- `.github/workflows/update-config-files.yml:11`

### script-injection (severity: high)

Sub-rule (a): e2e-tests.yml contains a run: block that directly interpolates a ${{ ... }} expression into the shell command string. The expression `${{ format('{0},{1}', toJSON(steps.stale.outputs.staled-issues-prs), toJSON(steps.stale.outputs.closed-issues-prs)) }}` is expanded by the GitHub Actions template engine before the shell sees it, allowing any attacker-controlled value in the step outputs to inject shell metacharacters. Offending line: `run: echo ${{ format('{0},{1}', toJSON(steps.stale.outputs.staled-issues-prs), toJSON(steps.stale.outputs.closed-issues-prs)) }}`

Locations:

- `.github/workflows/e2e-tests.yml:22`

### missing-permissions (severity: medium)

The following workflow files have no top-level permissions: key and no job-level permissions: key on any of their jobs, meaning they run with the default (potentially broad) token permissions: basic-validation.yml, check-dist.yml, codeql-analysis.yml, e2e-tests.yml, licensed.yml, update-config-files.yml.

Locations:

- `.github/workflows/basic-validation.yml:1`
- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/e2e-tests.yml:1`
- `.github/workflows/licensed.yml:1`
- `.github/workflows/update-config-files.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all findings across 8 workflow files:

1. unpinned-uses: Pinned all mutable references to full 40-char SHAs:
   - actions/reusable-workflows@main → @4735e71081024a944852f4ab9d1495b6dd2de8f2 # main (in basic-validation.yml, check-dist.yml, codeql-analysis.yml, licensed.yml, update-config-files.yml)
   - actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10 # v6 (in e2e-tests.yml, publish-immutable-actions.yml)
   - actions/publish-immutable-action@v0.0.4 → @4bc8754ffc40f27910afb20287dbbbb675a4e978 # v0.0.4 (in publish-immutable-actions.yml)
   - actions/publish-action@v0.4.0 → @23f4c6f12633a2da8f44938b71fde9afec138fb4 # v0.4.0 (in release-new-action-version.yml)

2. script-injection: Moved the ${{ format(...) }} expression in e2e-tests.yml out of the run: block into an env: block as STALE_OUTPUTS, then referenced it as "$STALE_OUTPUTS" in the shell command.

3. missing-permissions: Added `permissions: {}` top-level blocks to basic-validation.yml, check-dist.yml, codeql-analysis.yml, e2e-tests.yml, licensed.yml, and update-config-files.yml. publish-immutable-actions.yml and release-new-action-version.yml already had explicit permissions defined.

