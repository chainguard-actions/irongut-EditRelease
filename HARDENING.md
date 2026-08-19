<!-- markdownlint-disable -->

# Hardening Report: irongut--EditRelease/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **irongut--EditRelease/v1.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable tag-based `uses:` references instead of pinned 40-character SHA commits. This exposes the workflow to supply-chain attacks if the referenced action tag is moved or compromised.

ci-build.yml: actions/checkout@v2, actions/setup-dotnet@v1
mark-stale.yml: actions/stale@v4
release-build.yml: actions/checkout@v2, actions/setup-dotnet@v1, docker/login-action@v1, docker/metadata-action@v3, docker/build-push-action@v2

Additionally, action.yml references a Docker image by mutable tag instead of SHA digest:
  image: 'docker://ghcr.io/irongut/editrelease:v1.0.0'  (tag, not a SHA digest)

Locations:

- `.github/workflows/ci-build.yml:13`
- `.github/workflows/ci-build.yml:17`
- `.github/workflows/mark-stale.yml:11`
- `.github/workflows/release-build.yml:16`
- `.github/workflows/release-build.yml:20`
- `.github/workflows/release-build.yml:33`
- `.github/workflows/release-build.yml:38`
- `.github/workflows/release-build.yml:44`
- `action.yml:46`

### missing-permissions (severity: medium)

Three workflow files lack a top-level `permissions:` block and have at least one job without a job-level `permissions:` block, meaning those jobs run with the default (overly broad) token permissions.

- ci-build.yml: no top-level permissions; the `build` job has no permissions key.
- mark-stale.yml: no top-level permissions; the `stale` job has no permissions key.
- release-build.yml: no top-level permissions; the `build` job has no permissions key (only the `deploy` job defines permissions).

Locations:

- `.github/workflows/ci-build.yml:1`
- `.github/workflows/mark-stale.yml:1`
- `.github/workflows/release-build.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references by resolving to full SHA commits: actions/checkout@v2 → 0717577d, actions/setup-dotnet@v1 → 871f0413, actions/stale@v4 → a20b814f, docker/login-action@v1 → dd4fa067, docker/metadata-action@v3 → b2391d37, docker/build-push-action@v2 → ac9327ea. Pinned the Docker container image in action.yml to its sha256 digest while preserving the docker:// scheme and v1.0.0 tag. Added top-level `permissions: {}` to all three workflow files and added minimal job-level permissions: `contents: read` for build jobs, `issues: write` + `pull-requests: write` for the stale job, and `contents: read` + `packages: write` for the deploy job.

