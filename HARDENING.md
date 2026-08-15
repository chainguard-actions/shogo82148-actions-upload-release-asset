<!-- markdownlint-disable -->

# Hardening Report: shogo82148--actions-upload-release-asset/v1.10.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **shogo82148--actions-upload-release-asset/v1.10.4** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A `${{ ... }}` expression is directly interpolated inside a `run:` shell command. The step `run: echo ${{ steps.check.outputs.permission }}` injects the value of `steps.check.outputs.permission` directly into the shell command string before the shell ever sees it. An attacker who can influence that step output could inject arbitrary shell commands. The value should be passed via an `env:` variable and the shell variable should be double-quoted: `env:\n  PERMISSION: ${{ steps.check.outputs.permission }}\nrun: echo "$PERMISSION"`.

Locations:

- `.github/workflows/test.yml:35`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no individual job within any of these files defines a `permissions:` key either. Without explicit permissions, workflows inherit the repository's default token permissions, which may be overly broad (e.g., `write` on all scopes). Each workflow should declare minimal required permissions at the top level or per-job.

Locations:

- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/reviewdog.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed script-injection in test.yml by moving `${{ steps.check.outputs.permission }}` into an env variable `PERMISSION` and referencing it as `echo "$PERMISSION"` in the shell. Added `permissions: {}` top-level blocks to all 4 workflow files (test.yml, check-dist.yml, codeql-analysis.yml, reviewdog.yml). Added job-level permissions where needed: `contents: write` for the integrated job (creates/deletes releases and tags), `contents: read` + `security-events: write` for the CodeQL analyze job, `contents: read` for the check-dist job, and `contents: read` + `pull-requests: write` for both reviewdog jobs (eslint and actionlint use github-pr-review reporter).

