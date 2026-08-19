<!-- markdownlint-disable -->

# Hardening Report: tj-actions--auto-doc/v3.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **tj-actions--auto-doc/v3.4.0** was hardened automatically. 3 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Every `uses:` reference in action.yml and all workflow files is pinned to a mutable tag or branch rather than a full 40-character commit SHA, making the action vulnerable to supply-chain attacks. Failing references include: action.yml: tj-actions/setup-bin@v1; auto-approve.yml: hmarr/auto-approve-action@v3; codacy-analysis.yml: actions/checkout@v4, codacy/codacy-analysis-cli-action@v4.3.0, github/codeql-action/upload-sarif@v2; code-coverage.yml: actions/checkout@v4, actions/setup-go@v4, actions/cache@v3, codacy/codacy-coverage-reporter-action@v1, codecov/codecov-action@v3, tj-actions/coverage-badge-go@v2, tj-actions/verify-changed-files@v16, ad-m/github-push-action@master; codeql.yml: actions/checkout@v4, github/codeql-action/init@v2, github/codeql-action/autobuild@v2, github/codeql-action/analyze@v2; format-tidy.yml: rokroskar/workflow-run-cleanup-action@v0.3.3, actions/checkout@v4, actions/setup-go@v4, actions/cache@v3, tj-actions/verify-changed-files@v16, ad-m/github-push-action@master; greetings.yml: actions/first-interaction@v1; lint.yml: rokroskar/workflow-run-cleanup-action@v0.3.3, actions/checkout@v4, reviewdog/action-golangci-lint@v2; sync-release-version.yml: actions/checkout@v4, tj-actions/branch-names@v7, tj-actions/semver-diff@v2, actions/setup-go@v4, actions/cache@v3, goreleaser/goreleaser-action@v5, tj-actions/sync-release-version@v13, tj-actions/git-cliff@v1, tj-actions/verify-changed-files@v16, tj-actions/release-tagger@v4, peter-evans/create-pull-request@v5.0.2; test.yml: actions/checkout@v4, goreleaser/goreleaser-action@v5, actions/upload-artifact@v3, actions/setup-go@v4, tj-actions/remark@v3, tj-actions/verify-changed-files@v16, ad-m/github-push-action@master

Locations:

- `action.yml:56`
- `.github/workflows/auto-approve.yml:9`
- `.github/workflows/codacy-analysis.yml:28`
- `.github/workflows/code-coverage.yml:20`
- `.github/workflows/codeql.yml:35`
- `.github/workflows/format-tidy.yml:14`
- `.github/workflows/greetings.yml:8`
- `.github/workflows/lint.yml:18`
- `.github/workflows/sync-release-version.yml:12`
- `.github/workflows/test.yml:18`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any job, meaning they run with the default (potentially broad) GITHUB_TOKEN permissions: auto-approve.yml, codacy-analysis.yml, code-coverage.yml, format-tidy.yml, greetings.yml, lint.yml, sync-release-version.yml.

Locations:

- `.github/workflows/auto-approve.yml:1`
- `.github/workflows/codacy-analysis.yml:1`
- `.github/workflows/code-coverage.yml:1`
- `.github/workflows/format-tidy.yml:1`
- `.github/workflows/greetings.yml:1`
- `.github/workflows/lint.yml:1`
- `.github/workflows/sync-release-version.yml:1`

### script-injection (severity: high)

Multiple `run:` blocks directly interpolate `${{ ... }}` expressions (sub-rule a), allowing shell metacharacters to be injected before the shell ever sees the value. In sync-release-version.yml 'Show release type' step: `OLD_VERSION=$(echo "${{ steps.semver-diff.outputs.old_version }}" | ...)` and `MAJOR_VERSION=$(echo "${{ steps.semver-diff.outputs.new_version }}" | ...)`. In sync-release-version.yml 'Commit the change' step: `git tag -d ${{ steps.branch-name.outputs.tag }}`, `git commit -m "... ${{ steps.semver-diff.outputs.old_version }} -> ${{ steps.semver-diff.outputs.new_version }}"`, `git push -f origin ${{ steps.branch-name.outputs.tag }}`. In sync-release-version.yml 'Commit changes' (update-version job): `git tag -d ${{ steps.branch-name.outputs.tag }}`, `git add ${{ steps.verify-changed-files.outputs.changed_files }}`, `git commit -m "... ${{ steps.sync-release-version.outputs.old_version }} -> ..."`, `git push -f origin ${{ steps.branch-name.outputs.tag }}`. In format-tidy.yml 'Commit formatting changes' step: `git add cmd ${{ steps.verify-changed-files.outputs.changed_files }}`. In test.yml 'Commit README changes' step: `git add ${{ steps.verify-changed-files.outputs.changed_files }}`.

Locations:

- `.github/workflows/sync-release-version.yml:27`
- `.github/workflows/sync-release-version.yml:35`
- `.github/workflows/sync-release-version.yml:76`
- `.github/workflows/format-tidy.yml:52`
- `.github/workflows/test.yml:163`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings across 10 files:

1. **unpinned-uses**: Pinned all `uses:` references to full 40-character commit SHAs with original tags preserved as comments. Covered action.yml (tj-actions/setup-bin) and all 9 workflow files.

2. **missing-permissions**: Added minimal `permissions:` blocks to 7 workflow files: auto-approve.yml (pull-requests: write), codacy-analysis.yml (contents: read, security-events: write), code-coverage.yml (contents: write), format-tidy.yml (contents: write), greetings.yml (issues: write, pull-requests: write), lint.yml (contents: read), sync-release-version.yml (contents: write, pull-requests: write). codeql.yml already had job-level permissions.

3. **script-injection**: Moved all `${{ steps.*.outputs.* }}` expressions from `run:` shell scripts into `env:` blocks and referenced them as plain environment variables ($VAR_NAME). Fixed in sync-release-version.yml (5 injection points across 2 steps), format-tidy.yml (1 injection point), and test.yml (1 injection point). Files under tests/ were not modified.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed four unquoted shell variable expansions of workflow-controllable data:
1. sync-release-version.yml (line 37): Quoted OLD_VERSION and MAJOR_VERSION in the `make` command: `OLD_VERSION="$OLD_VERSION" MAJOR_VERSION="$MAJOR_VERSION"`
2. sync-release-version.yml (line 108): Quoted CHANGED_FILES in `git add "$CHANGED_FILES"`
3. test.yml (line 163): Quoted CHANGED_FILES in `git add "$CHANGED_FILES"`
4. format-tidy.yml (line 67): Quoted CHANGED_FILES in `git add cmd "$CHANGED_FILES"`

All variables were already being set via the step's `env:` block (moving ${{ }} expressions out of run: scripts), so only the quoting in the shell commands needed to be fixed.

### Iteration 3

**Fixes applied:** script-injection

**Notes:**

Converted EXTRA_ARGS from an unquoted string to a bash array in entrypoint.sh. All concatenations like `EXTRA_ARGS="${EXTRA_ARGS} --flag=${value}"` were replaced with `EXTRA_ARGS+=("--flag=${value}")`. The final command now uses `"${EXTRA_ARGS[@]}"` (quoted array expansion) instead of `${EXTRA_ARGS}` (unquoted string expansion). This prevents shell metacharacters in user-controlled inputs (repository, token, column names, etc.) from being interpreted as shell commands. The binary invocation was also quoted (`"$BIN_PATH"` instead of `$BIN_PATH`).

