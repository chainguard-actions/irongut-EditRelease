<!-- markdownlint-disable -->

# Hardening Report: irongut--EditRelease/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **irongut--EditRelease/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files and action.yml use mutable tag/version references instead of pinned full 40-character SHA commit hashes. This exposes the action to supply-chain attacks if the referenced tag is moved or the image is replaced.

Failing references:
- action.yml: `image: 'docker://ghcr.io/irongut/editrelease:v1.2.0'` (tag, not SHA digest)
- assign-author.yml: `uses: samspills/assign-pr-to-author@v1.0.2`
- assign-project.yml: `uses: srggrs/assign-one-project-github-action@1.3.1` (×4 steps)
- ci-build.yml: `uses: actions/checkout@v3`, `uses: actions/setup-dotnet@v2`
- mark-stale.yml: `uses: actions/stale@v5`
- pr-labeller.yml: `uses: actions/labeler@v4`
- release-build.yml: `uses: actions/checkout@v3`, `uses: actions/setup-dotnet@v2`, `uses: sigstore/cosign-installer@v2.4.1`, `uses: docker/setup-buildx-action@v2.0.0`, `uses: docker/login-action@v2`, `uses: docker/metadata-action@v4`, `uses: docker/build-push-action@v3`

Locations:

- `action.yml:47`
- `.github/workflows/assign-author.yml:11`
- `.github/workflows/assign-project.yml:15`
- `.github/workflows/ci-build.yml:13`
- `.github/workflows/mark-stale.yml:12`
- `.github/workflows/pr-labeller.yml:11`
- `.github/workflows/release-build.yml:15`

### script-injection (severity: high)

Rule (a) violation: The 'Sign the Docker image' step in release-build.yml directly interpolates GitHub Actions expressions inside a `run:` shell command string. Both `${{ steps.meta.outputs.tags }}` and `${{ steps.build-and-push.outputs.digest }}` are substituted into the shell command before the shell parses it, allowing a malicious value in those outputs to inject arbitrary shell commands.

Offending line:
  run: echo "${{ steps.meta.outputs.tags }}" | xargs -I {} cosign sign {}@${{ steps.build-and-push.outputs.digest }}

Locations:

- `.github/workflows/release-build.yml:57`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` block and at least one job also lacks a `permissions:` block. Without explicit permissions, workflows run with the repository's default token permissions (which may be overly broad, e.g. write access to contents).

- assign-author.yml: no top-level permissions; job 'assign-author' has no permissions block.
- assign-project.yml: no top-level permissions; job 'assign-project' has no permissions block.
- ci-build.yml: no top-level permissions; job 'build' has no permissions block.
- mark-stale.yml: no top-level permissions; job 'stale' has no permissions block.
- pr-labeller.yml: no top-level permissions; job 'label' has no permissions block. Note: this workflow uses the `pull_request_target` trigger, making missing permissions especially risky.
- release-build.yml: no top-level permissions; job 'build' has no permissions block (only the 'deploy' job has permissions).

Locations:

- `.github/workflows/assign-author.yml:1`
- `.github/workflows/assign-project.yml:1`
- `.github/workflows/ci-build.yml:1`
- `.github/workflows/mark-stale.yml:1`
- `.github/workflows/pr-labeller.yml:1`
- `.github/workflows/release-build.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three findings across 7 files:

1. unpinned-uses: Pinned all action references to full 40-char SHAs with tag comments. Pinned ghcr.io/irongut/editrelease:v1.2.0 container image to its sha256 digest in action.yml (preserving docker:// scheme and tag inline).

2. script-injection: In release-build.yml 'Sign the Docker image' step, moved ${{ steps.meta.outputs.tags }} and ${{ steps.build-and-push.outputs.digest }} out of the run: shell string into the step's env: block as META_TAGS and BUILD_DIGEST, then referenced them as plain shell variables.

3. missing-permissions: Added top-level `permissions: {}` to all 6 workflow files. Added minimal job-level permissions: contents: read for build jobs, pull-requests: write for PR workflows, issues: write for issue workflows, packages: write + id-token: write for the deploy job.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed the unquoted `$BUILD_DIGEST` variable in the 'Sign the Docker image' step of .github/workflows/release-build.yml. Replaced the `xargs -I {} cosign sign {}@$BUILD_DIGEST` command with a `while IFS= read -r tag; do cosign sign "${tag}@${BUILD_DIGEST}"; done <<< "$META_TAGS"` loop that properly double-quotes both variables, preventing shell metacharacter injection.

