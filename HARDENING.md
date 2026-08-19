<!-- markdownlint-disable -->

# Hardening Report: mshick--add-pr-comment/v3.10.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **mshick--add-pr-comment/v3.10.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Check for dogfood commit' step directly interpolates GitHub Actions expressions inside a run: shell command string. Specifically, `${{ github.repository }}` and `${{ github.event.pull_request.head.sha }}` are embedded directly in the shell command `MSG=$(gh api repos/${{ github.repository }}/commits/${{ github.event.pull_request.head.sha }} ...)`. These values flow through YAML template substitution before the shell parses them, allowing an attacker to inject shell metacharacters via a crafted repository name or PR SHA context. The values should be passed via env: variables and properly quoted in the shell.

Locations:

- `.github/workflows/ci.yml:97`

### script-injection (severity: high)

Sub-rule (a): The 'Tag Major and Minor Versions' step directly interpolates `${{ needs.release-please.outputs.major }}` and `${{ needs.release-please.outputs.minor }}` (needs.*.outputs.* context values) inside multiple shell commands in a run: block, e.g. `git tag -d v${{ needs.release-please.outputs.major }} || true`. These expressions are substituted by the YAML template engine before the shell sees them, meaning any shell metacharacters in the output values would be interpreted by the shell. These values should be passed via env: variables and double-quoted in the shell script.

Locations:

- `.github/workflows/release-please.yml:84`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script injection vulnerabilities:
1. `.github/workflows/ci.yml` (line 97): Moved `${{ github.repository }}` and `${{ github.event.pull_request.head.sha }}` out of the shell `run:` command and into `env:` variables (`REPO` and `HEAD_SHA`). The shell command now uses `"repos/$REPO/commits/$HEAD_SHA"` safely.
2. `.github/workflows/release-please.yml` (line 84): Moved `${{ needs.release-please.outputs.major }}` and `${{ needs.release-please.outputs.minor }}` out of all shell commands and into `env:` variables (`MAJOR` and `MINOR`). All git tag and push commands now reference `"$MAJOR"` and `"$MINOR"` with proper double-quoting.

