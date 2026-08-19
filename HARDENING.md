<!-- markdownlint-disable -->

# Hardening Report: mshick--add-pr-comment/v3.12.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **mshick--add-pr-comment/v3.12.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in run: block. The 'Check for dogfood commit' step interpolates `${{ github.repository }}` and `${{ github.event.pull_request.head.sha }}` directly inside a shell command string: `MSG=$(gh api repos/${{ github.repository }}/commits/${{ github.event.pull_request.head.sha }} --jq '...')`. These github.* context values are substituted by the Actions runner before the shell sees the command, enabling script injection if the values contain shell metacharacters. They should be passed via env: variables and double-quoted in the shell.

Locations:

- `.github/workflows/ci.yml:107`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in run: block. The 'Tag Major and Minor Versions' step interpolates `${{ needs.release-please.outputs.major }}` and `${{ needs.release-please.outputs.minor }}` (needs.*.outputs.* context values) directly inside multiple shell commands, e.g.: `git tag -d v${{ needs.release-please.outputs.major }} || true`. These values are substituted before the shell executes the command. They should be moved to env: variables and double-quoted in the shell.

Locations:

- `.github/workflows/release-please.yml:83`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script injection findings:
1. hardened/action/.github/workflows/ci.yml (line 107): Moved `${{ github.repository }}` and `${{ github.event.pull_request.head.sha }}` into env: variables (GH_REPOSITORY, PR_HEAD_SHA) and updated the gh api call to use double-quoted shell variable references.
2. hardened/action/.github/workflows/release-please.yml (line 83): Moved `${{ needs.release-please.outputs.major }}` and `${{ needs.release-please.outputs.minor }}` into env: variables (MAJOR, MINOR) and updated all git tag/push commands to use double-quoted shell variable references.

