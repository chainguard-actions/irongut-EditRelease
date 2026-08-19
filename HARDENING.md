<!-- markdownlint-disable -->

# Hardening Report: irongut--EditRelease/v1.1.0-beta

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **irongut--EditRelease/v1.1.0-beta** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files and action.yml use mutable tag/version references instead of pinned full SHA commits. This exposes the action to supply-chain attacks if the referenced tag is moved or the image is replaced.

Workflow unpinned uses:
- assign-author.yml: `samspills/assign-pr-to-author@v1.0.1`
- assign-project.yml: `srggrs/assign-one-project-github-action@1.3.1` (×4 steps)
- ci-build.yml: `actions/checkout@v2`, `actions/setup-dotnet@v1`
- mark-stale.yml: `actions/stale@v4`
- release-build.yml: `actions/checkout@v2`, `actions/setup-dotnet@v1`, `docker/login-action@v1`, `docker/metadata-action@v3`, `docker/build-push-action@v2`

action.yml docker image uses a mutable tag instead of a SHA digest:
- `image: 'docker://ghcr.io/irongut/editrelease:v1.1.0-beta'`

Locations:

- `.github/workflows/assign-author.yml:11`
- `.github/workflows/assign-project.yml:15`
- `.github/workflows/assign-project.yml:20`
- `.github/workflows/assign-project.yml:25`
- `.github/workflows/assign-project.yml:30`
- `.github/workflows/ci-build.yml:11`
- `.github/workflows/ci-build.yml:14`
- `.github/workflows/mark-stale.yml:11`
- `.github/workflows/release-build.yml:16`
- `.github/workflows/release-build.yml:19`
- `.github/workflows/release-build.yml:33`
- `.github/workflows/release-build.yml:38`
- `.github/workflows/release-build.yml:44`
- `.github/workflows/release-build.yml:49`
- `action.yml:44`

### missing-permissions (severity: medium)

Several workflow files have no top-level `permissions:` block and no job-level `permissions:` block on every job, meaning they run with the default (overly broad) token permissions.

- assign-author.yml: no permissions block at all.
- assign-project.yml: no permissions block at all.
- ci-build.yml: no permissions block at all.
- mark-stale.yml: no permissions block at all.
- release-build.yml: the `deploy` job has a permissions block, but the `build` job does not, and there is no top-level permissions block.

Locations:

- `.github/workflows/assign-author.yml:1`
- `.github/workflows/assign-project.yml:1`
- `.github/workflows/ci-build.yml:1`
- `.github/workflows/mark-stale.yml:1`
- `.github/workflows/release-build.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references by resolving each tag to its full commit SHA using lookup_action_sha. Pinned the docker container image in action.yml to its sha256 digest while preserving the docker:// scheme and tag. Added minimal permissions blocks to all 5 workflow files: assign-author.yml (pull-requests: write), assign-project.yml (issues: write, pull-requests: write), ci-build.yml (contents: read at top-level), mark-stale.yml (issues: write, pull-requests: write), and release-build.yml (contents: read on build job; deploy job already had contents: read + packages: write).

