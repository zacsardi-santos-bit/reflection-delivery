## Description

Several cluster management operations in Argo CD fail when the ArgoCD control plane secret is missing its encryption key. This can happen during startup or initialization before the key has been generated, as well as in degraded or partially-initialized states. The root cause is that the check for whether in-cluster deployments are allowed is currently routed through a heavier settings-loading mechanism that requires the encryption key — even though the in-cluster enabled flag only lives in the config map and has no dependency on the key.

## Expected Behavior

- Checking whether in-cluster deployments are allowed should read directly from the config map, independently of the encryption key or any other secret data
- When the config map is absent, the check should return the default value (enabled) along with an informational error rather than failing hard
- Listing clusters, creating clusters, watching for cluster changes, and querying cluster addresses by name should all continue working even when the encryption key is missing — logging a warning where appropriate instead of propagating an error
- If in-cluster mode is explicitly disabled in configuration, attempts to create an in-cluster cluster must still be rejected with an appropriate error
- If no in-cluster preference is set in the config map, the default behavior should be to treat in-cluster as enabled

## Why This Matters

Users encounter this issue during Argo CD startup or when the secret is temporarily missing its encryption key. Rather than failing with cryptic errors that block cluster operations, the system should degrade gracefully for operations that do not actually require the encryption key.
