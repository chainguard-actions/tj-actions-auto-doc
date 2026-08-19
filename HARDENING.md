<!-- markdownlint-disable -->

# Hardening Report: tj-actions--auto-doc/v3.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **tj-actions--auto-doc/v3.6.0** was hardened automatically. 15 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks directly interpolate `${{ steps.*.outputs.* }}` expressions, which are substituted by the Actions template engine before the shell sees them. An attacker who can influence step outputs (e.g. via a malicious action or crafted input) could inject arbitrary shell commands. Offending lines include:
- `OLD_VERSION=$(echo "${{ steps.semver-diff.outputs.old_version }}" | ...)` (sub-rule a)
- `MAJOR_VERSION=$(echo "${{ steps.semver-diff.outputs.new_version }}" | ...)` (sub-rule a)
- `git tag -d ${{ steps.branch-name.outputs.tag }}` (sub-rule a)
- `git commit -m "chore: upgraded from ${{ steps.semver-diff.outputs.old_version }} -> ${{ steps.semver-diff.outputs.new_version }}"` (sub-rule a)
- `git tag ${{ steps.branch-name.outputs.tag }}` (sub-rule a)
- `git push -f origin ${{ steps.branch-name.outputs.tag }}` (sub-rule a)
Fix: move values into `env:` variables and reference them as `"$VAR"` in the shell.

Locations:

- `.github/workflows/sync-release-version.yml:27`
- `.github/workflows/sync-release-version.yml:33`
- `.github/workflows/sync-release-version.yml:96`

### script-injection (severity: high)

`run:` block directly interpolates `${{ steps.verify-changed-files.outputs.changed_files }}` into a `git add` command (sub-rule a). An attacker who controls file names could inject shell metacharacters. Fix: assign to an `env:` variable and quote it in the shell.

Locations:

- `.github/workflows/format-tidy.yml:57`

### script-injection (severity: high)

`run:` block directly interpolates `${{ steps.verify-changed-files.outputs.changed_files }}` into a `git add` command (sub-rule a). An attacker who controls file names could inject shell metacharacters. Fix: assign to an `env:` variable and quote it in the shell.

Locations:

- `.github/workflows/test.yml:163`

### unpinned-uses (severity: high)

All `uses:` references in this workflow use mutable version tags instead of immutable 40-character commit SHAs, making the workflow vulnerable to supply-chain attacks if any referenced action is compromised or its tag is moved. Unpinned references include: `actions/checkout@v4`, `codacy/codacy-analysis-cli-action@v4.4.5`, `github/codeql-action/upload-sarif@v3`.

Locations:

- `.github/workflows/codacy-analysis.yml:29`

### unpinned-uses (severity: high)

All `uses:` references in this workflow use mutable version tags instead of immutable 40-character commit SHAs. Unpinned references include: `actions/checkout@v4`, `actions/setup-go@v5`, `actions/cache@v4`, `codacy/codacy-coverage-reporter-action@v1`, `codecov/codecov-action@v5`, `tj-actions/coverage-badge-go@v2`, `tj-actions/verify-changed-files@v20`, `ad-m/github-push-action@master`.

Locations:

- `.github/workflows/code-coverage.yml:21`

### unpinned-uses (severity: high)

All `uses:` references in this workflow use mutable version tags instead of immutable 40-character commit SHAs. Unpinned references include: `actions/checkout@v4`, `github/codeql-action/init@v3`, `github/codeql-action/autobuild@v3`, `github/codeql-action/analyze@v3`.

Locations:

- `.github/workflows/codeql.yml:36`

### unpinned-uses (severity: high)

All `uses:` references in this workflow use mutable version tags instead of immutable 40-character commit SHAs. Unpinned references include: `rokroskar/workflow-run-cleanup-action@v0.3.3`, `actions/checkout@v4`, `actions/setup-go@v5`, `actions/cache@v4`, `tj-actions/verify-changed-files@v20`, `ad-m/github-push-action@master`.

Locations:

- `.github/workflows/format-tidy.yml:14`

### unpinned-uses (severity: high)

All `uses:` references in this workflow use mutable version tags instead of immutable 40-character commit SHAs. Unpinned references include: `rokroskar/workflow-run-cleanup-action@v0.3.3`, `actions/checkout@v4`, `reviewdog/action-golangci-lint@v2`.

Locations:

- `.github/workflows/lint.yml:18`

### unpinned-uses (severity: high)

All `uses:` references in this workflow use mutable version tags instead of immutable 40-character commit SHAs. Unpinned references include: `actions/checkout@v4`, `tj-actions/branch-names@v8`, `tj-actions/semver-diff@v3`, `actions/setup-go@v5`, `actions/cache@v4`, `goreleaser/goreleaser-action@v5`, `tj-actions/sync-release-version@v13`, `tj-actions/git-cliff@v1`, `tj-actions/verify-changed-files@v20`, `tj-actions/release-tagger@v4`, `peter-evans/create-pull-request@v7.0.8`.

Locations:

- `.github/workflows/sync-release-version.yml:12`

### unpinned-uses (severity: high)

All `uses:` references in this workflow use mutable version tags instead of immutable 40-character commit SHAs. Unpinned references include: `actions/checkout@v4`, `actions/setup-go@v5`, `goreleaser/goreleaser-action@v5`, `actions/upload-artifact@v4`, `tj-actions/remark@v3`, `tj-actions/verify-changed-files@v20`, `ad-m/github-push-action@master`.

Locations:

- `.github/workflows/test.yml:17`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions (which may be `write-all`), granting broader access than necessary.

Locations:

- `.github/workflows/codacy-analysis.yml:1`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, granting broader access than necessary.

Locations:

- `.github/workflows/code-coverage.yml:1`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, granting broader access than necessary.

Locations:

- `.github/workflows/format-tidy.yml:1`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, granting broader access than necessary.

Locations:

- `.github/workflows/lint.yml:1`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, granting broader access than necessary.

Locations:

- `.github/workflows/sync-release-version.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all findings across 6 workflow files:

1. script-injection: Moved all ${{ steps.*.outputs.* }} expressions from run: blocks into env: blocks in sync-release-version.yml (3 steps fixed), format-tidy.yml (1 step fixed), and test.yml (1 step fixed).

2. unpinned-uses: Pinned all action references to full 40-character commit SHAs in codacy-analysis.yml, code-coverage.yml, codeql.yml, format-tidy.yml, lint.yml, sync-release-version.yml, and test.yml. Actions pinned include: actions/checkout@v4, actions/setup-go@v5, actions/cache@v4, actions/upload-artifact@v4, codacy/codacy-analysis-cli-action@v4.4.5, github/codeql-action/*@v3, codacy/codacy-coverage-reporter-action@v1, codecov/codecov-action@v5, tj-actions/coverage-badge-go@v2, tj-actions/verify-changed-files@v20, ad-m/github-push-action@master, rokroskar/workflow-run-cleanup-action@v0.3.3, reviewdog/action-golangci-lint@v2, tj-actions/branch-names@v8, tj-actions/semver-diff@v3, goreleaser/goreleaser-action@v5, tj-actions/sync-release-version@v13, tj-actions/git-cliff@v1, tj-actions/release-tagger@v4, peter-evans/create-pull-request@v7.0.8, tj-actions/remark@v3.

3. missing-permissions: Added top-level permissions blocks to codacy-analysis.yml (contents: read, security-events: write), code-coverage.yml (contents: write), format-tidy.yml (contents: write), lint.yml (contents: read), and sync-release-version.yml (contents: write). test.yml already had permissions: contents: write. codeql.yml already had job-level permissions.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted shell variable expansions in three workflow files:
1. format-tidy.yml (line 62): Quoted `$CHANGED_FILES` in `git add cmd "$CHANGED_FILES"`
2. sync-release-version.yml (line 33): Quoted `$OLD_VERSION` and `$MAJOR_VERSION` in the make command: `OLD_VERSION="$OLD_VERSION" MAJOR_VERSION="$MAJOR_VERSION"`
3. sync-release-version.yml (line 91): Quoted `$CHANGED_FILES` in `git add "$CHANGED_FILES"`
4. test.yml (line 183): Quoted `$CHANGED_FILES` in `git add "$CHANGED_FILES"`

The `$OLD_VERSION`/`$NEW_VERSION` variables in git commit messages (lines 47, 92) were already inside double-quoted strings (`-m "...$OLD_VERSION...$NEW_VERSION"`), so they were protected from word splitting. The primary injection vectors were the unquoted `git add` arguments and the unquoted make variable assignments.

