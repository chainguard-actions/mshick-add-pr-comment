<!-- markdownlint-disable -->

# Hardening Report: mshick--add-pr-comment/v3.9.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **mshick--add-pr-comment/v3.9.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Check for dogfood commit' run: block in ci.yml directly interpolates ${{ github.repository }} and ${{ github.event.pull_request.head.sha }} inside a shell command string. These expressions are substituted by the GitHub Actions template engine before the shell ever sees them, allowing an attacker to inject shell metacharacters via a crafted repository name. The offending line is: `MSG=$(gh api repos/${{ github.repository }}/commits/${{ github.event.pull_request.head.sha }} --jq '...')`

Locations:

- `.github/workflows/ci.yml:92`

### script-injection (severity: high)

Sub-rule (a): The 'Tag Major and Minor Versions' run: block in release-please.yml directly interpolates ${{ needs.release-please.outputs.major }} and ${{ needs.release-please.outputs.minor }} inside multiple shell commands (git tag, git push). These are needs.*.outputs.* values — a workflow-controllable context — substituted directly into shell before quoting, enabling shell metacharacter injection. Example offending lines: `git tag -d v${{ needs.release-please.outputs.major }} || true` and `git tag -d v${{ needs.release-please.outputs.major }}.${{ needs.release-please.outputs.minor }} || true`

Locations:

- `.github/workflows/release-please.yml:82`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script injection vulnerabilities:
1. ci.yml (line 92): Moved `${{ github.repository }}` and `${{ github.event.pull_request.head.sha }}` into the step's `env:` block as `REPO` and `HEAD_SHA`. The shell script now uses `$REPO` and `$HEAD_SHA` as plain environment variables, preventing template-engine substitution before the shell sees the command.
2. release-please.yml (line 82): Moved `${{ needs.release-please.outputs.major }}` and `${{ needs.release-please.outputs.minor }}` into the step's `env:` block as `MAJOR` and `MINOR`. All git tag and git push commands now reference `$MAJOR` and `$MINOR` as environment variables with proper double-quoting, preventing shell metacharacter injection.

