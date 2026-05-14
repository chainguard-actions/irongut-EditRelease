# Hardening Report: irongut--EditRelease/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **irongut--EditRelease/v1.2.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml runs.image field references a Docker image using a mutable version tag instead of an immutable SHA digest. The reference 'docker://ghcr.io/irongut/editrelease:v1.2.0' uses the tag 'v1.2.0', which can be silently replaced with a different (potentially malicious) image without any change to the action configuration. It should be pinned to a SHA digest, e.g. 'docker://ghcr.io/irongut/editrelease@sha256:<64-hex-char-digest>'.

Locations:

- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag 'docker://ghcr.io/irongut/editrelease:v1.2.0' with the immutable SHA256 digest 'docker://ghcr.io/irongut/editrelease@sha256:a68ecd1ac7ba32ca07bc03b069f250200cf9e532e9496872aa4a3cb1d82bb167' in action.yml line 44. The original tag is preserved as a comment for readability.

