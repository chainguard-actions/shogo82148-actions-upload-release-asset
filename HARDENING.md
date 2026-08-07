<!-- markdownlint-disable -->

# Hardening Report: shogo82148--actions-upload-release-asset/v1.10.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **shogo82148--actions-upload-release-asset/v1.10.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated inside a run: shell command. In test.yml, the step 'the result' runs `echo ${{ steps.check.outputs.permission }}`, which injects the step output value directly into the shell command string before the shell ever sees it. If the output contains shell metacharacters, this can lead to command injection.

Locations:

- `.github/workflows/test.yml:33`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no individual job within any of these files defines a job-level `permissions:` block. Without explicit permissions, workflows run with the repository's default token permissions, which may be overly broad (e.g., write access to contents, pull-requests, etc.).

Locations:

- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/reviewdog.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed script-injection in test.yml by moving `${{ steps.check.outputs.permission }}` into an env: block (PERMISSION) and referencing it as `"$PERMISSION"` in the shell. Added top-level `permissions: {}` to all 4 workflow files (test.yml, check-dist.yml, codeql-analysis.yml, reviewdog.yml), plus job-level permissions with minimal required access: contents:read for most jobs, contents:write for the integrated test job (creates/deletes releases and tags), security-events:write for CodeQL analysis, and pull-requests:write for reviewdog jobs.

