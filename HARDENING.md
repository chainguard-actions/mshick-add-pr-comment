<!-- markdownlint-disable -->

# Hardening Report: mshick--add-pr-comment/v3.9.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **mshick--add-pr-comment/v3.9.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: The 'Check for dogfood commit' step directly interpolates GitHub Actions expressions into a run: shell command string. The line `MSG=$(gh api repos/${{ github.repository }}/commits/${{ github.event.pull_request.head.sha }} --jq '...')` embeds `${{ github.repository }}` and `${{ github.event.pull_request.head.sha }}` directly into the shell command. These values are substituted by the YAML template engine before the shell ever sees them, allowing an attacker to inject shell metacharacters via a crafted repository name or commit SHA reference. These should be passed via env: variables and double-quoted in the shell.

Locations:

- `.github/workflows/ci.yml:80`

### script-injection (severity: high)

Rule (a) violation: The 'Tag Major and Minor Versions' step directly interpolates `needs.*.outputs.*` expressions into run: shell commands. Lines such as `git tag -d v${{ needs.release-please.outputs.major }}`, `git push origin :v${{ needs.release-please.outputs.major }}.${{ needs.release-please.outputs.minor }}`, and the corresponding `git tag -a` commands embed `${{ needs.release-please.outputs.major }}` and `${{ needs.release-please.outputs.minor }}` directly into shell commands. Per the check rules, `needs.*.outputs.*` values are untrusted inputs. If the release-please action's outputs were ever tampered with or contained shell metacharacters, this could lead to command injection. These values should be passed via env: variables and double-quoted.

Locations:

- `.github/workflows/release-please.yml:87`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script injection findings:
1. hardened/action/.github/workflows/ci.yml (line 80): Moved `${{ github.repository }}` and `${{ github.event.pull_request.head.sha }}` out of the `run:` shell command and into the step's `env:` block as `REPO` and `HEAD_SHA`. The shell script now references `"$REPO"` and `"$HEAD_SHA"` safely.
2. hardened/action/.github/workflows/release-please.yml (line 87): Moved `${{ needs.release-please.outputs.major }}` and `${{ needs.release-please.outputs.minor }}` out of the `run:` shell commands and into the step's `env:` block as `MAJOR` and `MINOR`. All git tag/push commands now reference `"$MAJOR"` and `"$MINOR"` with proper double-quoting.

