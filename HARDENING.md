<!-- markdownlint-disable -->

# Hardening Report: peter-evans--create-issue-from-file/v6.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **peter-evans--create-issue-from-file/v6.0.0** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in run: blocks. In ciff-example-command.yml, the 'Get the target repository and branch' step interpolates ${{ github.event.client_payload.slash_command.repository }}, ${{ github.repository }}, and ${{ github.event.client_payload.slash_command.branch }} directly inside shell commands. These values flow from repository_dispatch payloads which can be attacker-controlled. Offending lines: `repository=${{ github.event.client_payload.slash_command.repository }}`, `repository=${{ github.repository }}`, `branch=${{ github.event.client_payload.slash_command.branch }}`.

Locations:

- `.github/workflows/ciff-example-command.yml:13`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in run: blocks. In update-major-version.yml, workflow_dispatch inputs are interpolated directly into shell commands: `git tag -f ${{ github.event.inputs.main_version }} ${{ github.event.inputs.target }}` and `git push origin ${{ github.event.inputs.main_version }} --force`. An attacker with dispatch access could inject arbitrary shell commands via these inputs.

Locations:

- `.github/workflows/update-major-version.yml:27`
- `.github/workflows/update-major-version.yml:29`

### github-env-injection (severity: high)

Values derived from untrusted github.event.client_payload.* context are written to $GITHUB_OUTPUT without sanitization. The run: block assigns `${{ github.event.client_payload.slash_command.repository }}` and `${{ github.event.client_payload.slash_command.branch }}` to shell variables, then writes them with `echo "repository=$repository" >> $GITHUB_OUTPUT` and `echo "branch=$branch" >> $GITHUB_OUTPUT` without applying `printf '%s' ... | tr -d '\n\r'` sanitization. A newline in the payload value could inject arbitrary output entries.

Locations:

- `.github/workflows/ciff-example-command.yml:15`
- `.github/workflows/ciff-example-command.yml:18`

### missing-permissions (severity: medium)

Workflow file has no top-level permissions: key and no job-level permissions: key on any job. Without explicit permissions, the workflow inherits the repository default (often write-all for private repos or read-all for public repos), violating the principle of least privilege.

Locations:

- `.github/workflows/automerge-dependabot.yml:1`

### missing-permissions (severity: medium)

Workflow file has no top-level permissions: key and no job-level permissions: key on any job. Without explicit permissions, the workflow inherits the repository default, violating the principle of least privilege.

Locations:

- `.github/workflows/ciff-example-command.yml:1`

### missing-permissions (severity: medium)

Workflow file has no top-level permissions: key and no job-level permissions: key on any job. Without explicit permissions, the workflow inherits the repository default, violating the principle of least privilege.

Locations:

- `.github/workflows/slash-command-dispatch.yml:1`

### missing-permissions (severity: medium)

Workflow file has no top-level permissions: key and no job-level permissions: key on any job. Without explicit permissions, the workflow inherits the repository default, violating the principle of least privilege.

Locations:

- `.github/workflows/update-major-version.yml:1`

### unpinned-uses (severity: high)

All uses: references in this workflow use mutable version tags instead of full 40-character SHA commit hashes, making the workflow vulnerable to supply-chain attacks if those tags are moved. Unpinned references: `peter-evans/enable-pull-request-automerge@v3`.

Locations:

- `.github/workflows/automerge-dependabot.yml:8`

### unpinned-uses (severity: high)

All uses: references in this workflow use mutable version tags instead of full 40-character SHA commit hashes. Unpinned references: `actions/checkout@v5`, `actions/setup-node@v5`, `actions/upload-artifact@v4` (×2), `actions/download-artifact@v5` (×2), `peter-evans/close-issue@v3`, `peter-evans/create-pull-request@v7`.

Locations:

- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:32`
- `.github/workflows/ci.yml:42`
- `.github/workflows/ci.yml:46`
- `.github/workflows/ci.yml:68`
- `.github/workflows/ci.yml:79`

### unpinned-uses (severity: high)

All uses: references in this workflow use mutable version tags instead of full 40-character SHA commit hashes. Unpinned references: `actions/checkout@v5`, `peter-evans/create-or-update-comment@v4`.

Locations:

- `.github/workflows/ciff-example-command.yml:20`
- `.github/workflows/ciff-example-command.yml:48`

### unpinned-uses (severity: high)

The uses: reference in this workflow uses a mutable version tag instead of a full 40-character SHA commit hash. Unpinned reference: `peter-evans/slash-command-dispatch@v4`.

Locations:

- `.github/workflows/slash-command-dispatch.yml:8`

### unpinned-uses (severity: high)

The uses: reference in this workflow uses a mutable version tag instead of a full 40-character SHA commit hash. Unpinned reference: `actions/checkout@v5`.

Locations:

- `.github/workflows/update-major-version.yml:18`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, missing-permissions, unpinned-uses

**Notes:**

Fixed all findings across 5 workflow files:

1. automerge-dependabot.yml: Added `permissions: pull-requests: write`; pinned peter-evans/enable-pull-request-automerge@v3 to SHA a660677d.

2. ciff-example-command.yml: Added `permissions: issues: write, contents: read`; moved client_payload expressions into env: block to fix script-injection; sanitized values with `printf '%s' | tr -d '\n\r'` before writing to GITHUB_OUTPUT to fix github-env-injection; pinned actions/checkout@v5 and peter-evans/create-or-update-comment@v4 to full SHAs.

3. ci.yml: Pinned all 8 unpinned action references (actions/checkout@v5, actions/setup-node@v5, actions/upload-artifact@v4 ×2, actions/download-artifact@v5 ×3, peter-evans/close-issue@v3, peter-evans/create-pull-request@v7) to full commit SHAs. File already had permissions block.

4. slash-command-dispatch.yml: Added `permissions: issues: read, pull-requests: read`; pinned peter-evans/slash-command-dispatch@v4 to SHA 13bc0976.

5. update-major-version.yml: Added `permissions: contents: write`; moved workflow_dispatch inputs into env: blocks for the git tag and git push steps to fix script-injection; pinned actions/checkout@v5 to full SHA.

