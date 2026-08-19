<!-- markdownlint-disable -->

# Hardening Report: irongut--EditRelease/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **irongut--EditRelease/v1.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a mutable Docker image tag instead of a SHA digest: `image: 'docker://ghcr.io/irongut/editrelease:v1.1.0'`. This is vulnerable to supply-chain attacks if the tag is moved. All six workflow files also use tag-based (non-SHA) `uses:` references: assign-author.yml uses `samspills/assign-pr-to-author@v1.0.1`; assign-project.yml uses `srggrs/assign-one-project-github-action@1.3.1` (×4); ci-build.yml uses `actions/checkout@v2` and `actions/setup-dotnet@v1`; mark-stale.yml uses `actions/stale@v4`; pr-labeller.yml uses `actions/labeler@v3`; release-build.yml uses `actions/checkout@v2`, `actions/setup-dotnet@v1`, `docker/login-action@v1`, `docker/metadata-action@v3`, and `docker/build-push-action@v2`. All should be pinned to full 40-character commit SHAs.

Locations:

- `action.yml:43`
- `.github/workflows/assign-author.yml:11`
- `.github/workflows/assign-project.yml:15`
- `.github/workflows/ci-build.yml:14`
- `.github/workflows/ci-build.yml:19`
- `.github/workflows/mark-stale.yml:11`
- `.github/workflows/pr-labeller.yml:11`
- `.github/workflows/release-build.yml:16`
- `.github/workflows/release-build.yml:21`
- `.github/workflows/release-build.yml:35`
- `.github/workflows/release-build.yml:41`
- `.github/workflows/release-build.yml:48`
- `.github/workflows/release-build.yml:53`

### missing-permissions (severity: medium)

Multiple workflow files have no top-level `permissions:` block and no job-level `permissions:` blocks, meaning they run with the default (potentially broad) token permissions. `assign-author.yml`, `assign-project.yml`, `ci-build.yml`, `mark-stale.yml`, and `pr-labeller.yml` have no permissions declared at any level. `release-build.yml` has no top-level permissions and its `build` job has no job-level permissions (only the `deploy` job does). Each workflow should declare minimal specific permissions.

Locations:

- `.github/workflows/assign-author.yml:1`
- `.github/workflows/assign-project.yml:1`
- `.github/workflows/ci-build.yml:1`
- `.github/workflows/mark-stale.yml:1`
- `.github/workflows/pr-labeller.yml:1`
- `.github/workflows/release-build.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references by resolving to full 40-char commit SHAs: samspills/assign-pr-to-author@223a87a, srggrs/assign-one-project-github-action@65a8dda (×4), actions/checkout@0717577 (×3), actions/setup-dotnet@871f041 (×2), actions/stale@a20b814, actions/labeler@26546f6, docker/login-action@dd4fa06, docker/metadata-action@b2391d3, docker/build-push-action@ac9327e. Pinned the Docker container image in action.yml to its sha256 digest while preserving the docker:// scheme and v1.1.0 tag. Added minimal permissions blocks to all six workflow files: assign-author.yml (pull-requests: write), assign-project.yml (repository-projects: write), ci-build.yml (contents: read), mark-stale.yml (issues: write, pull-requests: write), pr-labeller.yml (contents: read, pull-requests: write), release-build.yml (top-level contents: read, build job contents: read, deploy job contents: read + packages: write).

