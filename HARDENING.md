<!-- markdownlint-disable -->

# Hardening Report: irongut--EditRelease/v1.2.1-alpha

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **irongut--EditRelease/v1.2.1-alpha** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: The 'Sign the Docker image' step in release-build.yml directly interpolates `${{ steps.meta.outputs.tags }}` and `${{ steps.build-and-push.outputs.digest }}` inside a `run:` shell command string. These are `steps.*.outputs.*` values (workflow-controllable context) that flow through YAML template substitution before the shell sees them, enabling command injection. Offending line: `run: echo "${{ steps.meta.outputs.tags }}" | xargs -I {} cosign sign {}@${{ steps.build-and-push.outputs.digest }}`

Locations:

- `.github/workflows/release-build.yml:88`

### missing-permissions (severity: medium)

The workflow file assign-author.yml has no top-level `permissions:` key, and its only job (`assign-author`) also has no job-level `permissions:` key. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially write) permissions, violating least-privilege.

Locations:

- `.github/workflows/assign-author.yml:1`

### unpinned-uses (severity: high)

action.yml references the Docker image `docker://ghcr.io/irongut/editrelease:v1.2.0` using a mutable version tag (`v1.2.0`) instead of an immutable SHA digest (e.g. `ghcr.io/irongut/editrelease@sha256:<64-hex-char-digest>`). A mutable tag can be silently replaced with a different image, enabling supply-chain attacks.

Locations:

- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions, unpinned-uses

**Notes:**

1. script-injection (release-build.yml line 88): Moved `${{ steps.meta.outputs.tags }}` and `${{ steps.build-and-push.outputs.digest }}` out of the `run:` shell string into the step's `env:` block as `DOCKER_TAGS` and `DOCKER_DIGEST`. The shell command now references `$DOCKER_TAGS` and `$DOCKER_DIGEST` as plain environment variables.
2. missing-permissions (assign-author.yml): Added a top-level `permissions: pull-requests: write` block. The workflow assigns PRs to their authors, which requires pull-requests write access; all other permissions default to none.
3. unpinned-uses (action.yml line 44): Pinned the Docker image from the mutable tag `v1.2.0` to the immutable digest `sha256:a68ecd1ac7ba32ca07bc03b069f250200cf9e532e9496872aa4a3cb1d82bb167`, preserving the `docker://` scheme and the version tag inline as `docker://ghcr.io/irongut/editrelease:v1.2.0@sha256:a68ecd1ac7ba32ca07bc03b069f250200cf9e532e9496872aa4a3cb1d82bb167`.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted `$DOCKER_DIGEST` variable in the 'Sign the Docker image' step of `.github/workflows/release-build.yml`. Changed `{}@$DOCKER_DIGEST` to `{}@"$DOCKER_DIGEST"` so the variable is properly quoted, preventing shell metacharacters in the workflow-controllable `steps.build-and-push.outputs.digest` value from being interpreted by the shell.

