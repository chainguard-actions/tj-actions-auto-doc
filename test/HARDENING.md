<!-- markdownlint-disable -->

# Hardening Report: tj-actions--auto-doc--test/v3.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **tj-actions--auto-doc--test/v3.4.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Both reusable workflow files reference `actions/labeler@v4`, which is a mutable tag reference rather than a pinned 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without any change to the workflow file. Each file should pin to a full SHA, e.g. `actions/labeler@<40-hex-sha> # v4`.

Locations:

- `reusable-action.yml:40`
- `reusable-workflow-no-inputs-no-outputs.yml:9`

### missing-permissions (severity: medium)

Neither `reusable-action.yml` nor `reusable-workflow-no-inputs-no-outputs.yml` declares a top-level `permissions:` block, and none of their individual jobs declare job-level `permissions:` blocks. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions (often `write-all` for older repositories), granting broader access than necessary. Each workflow should declare minimal required permissions at the top level or per job.

Locations:

- `reusable-action.yml:1`
- `reusable-workflow-no-inputs-no-outputs.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed both reusable-action.yml and reusable-workflow-no-inputs-no-outputs.yml:
1. Pinned actions/labeler@v4 to full SHA actions/labeler@ac9175f8a1f3625fd0d4fb234536d26811351594 # v4 in both files.
2. Added top-level `permissions: {}` block to both files to deny all permissions by default.
3. Added job-level permissions: the `triage` job (which runs actions/labeler) gets `contents: read` and `pull-requests: write` (minimum required for labeler to read repo contents and apply labels via PR API); the `example_job` gets `permissions: {}` since it only runs echo commands.

