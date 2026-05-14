# Hardening Report: irongut--EditRelease/v1.1.0-beta

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **irongut--EditRelease/v1.1.0-beta** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image reference with a mutable version tag instead of an immutable SHA digest. The image `docker://ghcr.io/irongut/editrelease:v1.1.0-beta` uses the tag `v1.1.0-beta`, which can be changed at any time to point to a different (potentially malicious) image. It should be pinned to a specific SHA256 digest, e.g. `docker://ghcr.io/irongut/editrelease@sha256:<64-hex-char-digest> # v1.1.0-beta`.

Locations:

- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced mutable Docker image tag `ghcr.io/irongut/editrelease:v1.1.0-beta` with immutable digest `ghcr.io/irongut/editrelease@sha256:4a43be223b763c2285198789ad351887b3140cdacf0ac1c01a13e1ff7f66d41b` in action.yml line 44. The original tag is preserved as a comment outside the YAML quotes.

