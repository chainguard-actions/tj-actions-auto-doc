<!-- markdownlint-disable -->

# Hardening Report: tj-actions--auto-doc/v3.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **tj-actions--auto-doc/v3.4.0** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action step uses `tj-actions/setup-bin@v1`, which is a mutable version tag rather than a pinned 40-character commit SHA. This means the referenced action could be silently replaced with a different (potentially malicious) version without any change to this file, creating a supply-chain attack risk.

Locations:

- `action.yml:56`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned `tj-actions/setup-bin@v1` to its full commit SHA `cc810cdd7ca2809436d6cd0c03614b049e787071` in hardened/action/action.yml (line 56). The mutable `v1` tag is preserved as an inline comment for readability.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed both script injection issues in entrypoint.sh:
1. Quoted `$BIN_PATH` as `"$BIN_PATH"` in the command invocation to prevent word-splitting on the binary path (which is derived from user-controlled `inputs.bin_path`).
2. Converted `EXTRA_ARGS` from a concatenated string (expanded unquoted with `${EXTRA_ARGS}`) to a proper bash array. Each flag is now appended as a discrete array element via `EXTRA_ARGS+=("--flag=value")` and expanded with `"${EXTRA_ARGS[@]}"`. This ensures user-controlled values like `--repository=${INPUT_REPOSITORY}` and `--token=${INPUT_TOKEN}` cannot inject additional arguments or commands via embedded whitespace or shell metacharacters. The `# shellcheck disable=SC2086` comment was removed as it is no longer needed.

