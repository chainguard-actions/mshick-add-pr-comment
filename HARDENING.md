<!-- markdownlint-disable -->

# Hardening Report: mshick--add-pr-comment/v3.11.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **mshick--add-pr-comment/v3.11.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in a run: block. The 'Check for dogfood commit' step in the dogfood job interpolates `${{ github.repository }}` and `${{ github.event.pull_request.head.sha }}` directly inside a shell command string: `MSG=$(gh api repos/${{ github.repository }}/commits/${{ github.event.pull_request.head.sha }} --jq '...')`. An attacker who controls the repository name or PR SHA metadata could inject shell metacharacters. These values should be passed via env: variables and referenced as quoted `"$VAR"` shell variables instead.

Locations:

- `.github/workflows/ci.yml:108`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in a run: block. The 'Tag Major and Minor Versions' step in the tag job interpolates `${{ needs.release-please.outputs.major }}` and `${{ needs.release-please.outputs.minor }}` directly inside shell commands such as `git tag -d v${{ needs.release-please.outputs.major }} || true` and `git push origin v${{ needs.release-please.outputs.major }}.${{ needs.release-please.outputs.minor }}`. Although these values originate from release-please outputs and are likely numeric, any `${{ ... }}` expression inside a run: block is a script-injection risk. These should be moved to env: variables and referenced as quoted shell variables.

Locations:

- `.github/workflows/release-please.yml:92`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script-injection findings:
1. `.github/workflows/ci.yml` (line 108): Moved `${{ github.repository }}` and `${{ github.event.pull_request.head.sha }}` into the step's `env:` block as `GITHUB_REPOSITORY` and `PR_HEAD_SHA`, then referenced them as quoted shell variables in the `run:` block.
2. `.github/workflows/release-please.yml` (line 92): Moved `${{ needs.release-please.outputs.major }}` and `${{ needs.release-please.outputs.minor }}` into the step's `env:` block as `MAJOR` and `MINOR`, then referenced them as quoted shell variables throughout all git tag and push commands.

