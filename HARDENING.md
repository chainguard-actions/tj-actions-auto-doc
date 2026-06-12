<!-- markdownlint-disable -->

# Hardening Report: tj-actions--auto-doc/v3.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **tj-actions--auto-doc/v3.4.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references 'tj-actions/setup-bin@v1' using a mutable tag (@v1) instead of a full 40-character commit SHA. This means the action could be silently updated to a malicious version without any change to the workflow file, enabling a supply-chain attack.

Locations:

- `action.yml:56`

### script-injection (severity: high)

Rule (b) violation: entrypoint.sh expands several workflow-controlled environment variables (set from inputs.* via the env: block) without double-quoting them inside shell command strings. Unquoted expansions allow shell metacharacters (;, |, &, $(...), whitespace, globs) embedded in attacker-controlled input values to be interpreted by the shell.

Specific unquoted expansions:
- Line ~14: `--inputColumns=${input_column}` (from inputs.input_columns)
- Line ~22: `--outputColumns=${output_column}` (from inputs.output_columns)
- Line ~30: `--reusableSecretColumns=${reusable_secret_column}` (from inputs.reusable_secret_columns)
- Line ~38: `--reusableInputColumns=${reusable_input_column}` (from inputs.reusable_input_columns)
- Line ~46: `--reusableOutputColumns=${reusable_output_column}` (from inputs.reusable_output_columns)
- Line ~72: `--repository=${INPUT_REPOSITORY}` (from inputs.repository / github.repository)
- Line ~77: `--token=${INPUT_TOKEN}` (from inputs.token / github.token)
- Line ~88: `$BIN_PATH` (from inputs.bin_path / steps output) is unquoted at the binary execution line

All of these should use double-quoted forms such as `"${INPUT_REPOSITORY}"` to prevent word-splitting and glob expansion.

Locations:

- `entrypoint.sh:14`
- `entrypoint.sh:22`
- `entrypoint.sh:30`
- `entrypoint.sh:38`
- `entrypoint.sh:46`
- `entrypoint.sh:72`
- `entrypoint.sh:77`
- `entrypoint.sh:88`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. action.yml: Pinned `tj-actions/setup-bin@v1` to full SHA `cc810cdd7ca2809436d6cd0c03614b049e787071` with `# v1` comment preserved. 2. entrypoint.sh: Converted `EXTRA_ARGS` from an unquoted string to a bash array (`EXTRA_ARGS=()`). All column values, repository, and token arguments are now appended as properly quoted array elements using `EXTRA_ARGS+=("--flag=${value}")`. The binary is invoked with `"$BIN_PATH"` (double-quoted) and `"${EXTRA_ARGS[@]}"` (properly expanded array), eliminating all word-splitting and glob expansion vulnerabilities. The `# shellcheck disable=SC2086` comment was removed as it is no longer needed.

