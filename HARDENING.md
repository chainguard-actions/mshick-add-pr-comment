<!-- markdownlint-disable -->

# Hardening Report: mshick--add-pr-comment/v3.10.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **mshick--add-pr-comment/v3.10.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Check for dogfood commit' run: block in ci.yml directly interpolates GitHub Actions expressions inside a shell command string. Specifically, `${{ github.repository }}` and `${{ github.event.pull_request.head.sha }}` are embedded directly in the shell command `MSG=$(gh api repos/${{ github.repository }}/commits/${{ github.event.pull_request.head.sha }} ...)`. These expressions are substituted by the Actions runner before the shell ever sees them, allowing an attacker who controls the PR head SHA or repository name to inject arbitrary shell commands.

Locations:

- `.github/workflows/ci.yml:57`

### script-injection (severity: high)

Sub-rule (a): The 'Tag Major and Minor Versions' run: block in release-please.yml directly interpolates `needs.*.outputs.*` expressions inside shell commands. Values such as `${{ needs.release-please.outputs.major }}` and `${{ needs.release-please.outputs.minor }}` are embedded directly in git tag and git push commands (e.g., `git tag -d v${{ needs.release-please.outputs.major }}`). These step/job output values flow through YAML template substitution before the shell processes them, enabling script injection if the upstream release-please step outputs are ever tampered with.

Locations:

- `.github/workflows/release-please.yml:79`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script injection findings:
1. `.github/workflows/ci.yml` (line 57): Moved `${{ github.repository }}` and `${{ github.event.pull_request.head.sha }}` out of the `run:` shell string and into the step's `env:` block as `REPO` and `HEAD_SHA`. The shell command now references `$REPO` and `$HEAD_SHA` as plain environment variables.
2. `.github/workflows/release-please.yml` (line 79): Moved `${{ needs.release-please.outputs.major }}` and `${{ needs.release-please.outputs.minor }}` out of the `run:` shell string and into the step's `env:` block as `MAJOR` and `MINOR`. All git tag and git push commands now reference `${MAJOR}` and `${MINOR}` as plain environment variables.

