# Hardening Report: irongut--EditRelease/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **irongut--EditRelease/v1.0.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image reference with a mutable tag instead of a SHA digest. `image: 'docker://ghcr.io/irongut/editrelease:v1.0.0'` uses the tag `v1.0.0`, which can be changed at any time to point to a different (potentially malicious) image. It should be pinned to a specific SHA256 digest, e.g. `image: 'docker://ghcr.io/irongut/editrelease@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker image reference in action.yml from `docker://ghcr.io/irongut/editrelease:v1.0.0` to `docker://ghcr.io/irongut/editrelease@sha256:dfcb52e0f7ac47ab56f62c67ceb81e593fc2c4e3bfabe4cdc5dcc405903ae564` with a `# v1.0.0` comment to preserve readability. The digest was resolved via the Docker Registry HTTP API v2.

