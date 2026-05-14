# Hardening Report: irongut--EditRelease/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **irongut--EditRelease/v1.1.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image reference with a mutable version tag (`docker://ghcr.io/irongut/editrelease:v1.1.0`) instead of an immutable SHA digest. This means the image pulled at runtime could change without notice, enabling supply-chain attacks. It should be pinned to a specific SHA digest, e.g. `docker://ghcr.io/irongut/editrelease@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:49`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag `docker://ghcr.io/irongut/editrelease:v1.1.0` with the immutable SHA256 digest `docker://ghcr.io/irongut/editrelease@sha256:09249d3ea9ebd713d00eb246efb4df23b68b2cfa05921c21c4f1663630318383` in action.yml line 49. The original tag is preserved as a YAML comment `# v1.1.0` outside the quoted string for readability.

