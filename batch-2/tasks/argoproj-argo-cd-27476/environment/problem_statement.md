## Description

Cluster management operations fail entirely when the Argo CD system secret is missing its main cryptographic key. Registering a cluster, listing clusters, watching for cluster changes, and resolving cluster names all go through a heavy settings-loading path that validates and requires this key to be present. If it's absent — which can happen during bootstrapping or in certain deployment configurations — every one of these operations returns an error, even though they don't fundamentally need that key.

The in-cluster access check (whether the local Kubernetes API server is allowed as a cluster) is just a single boolean read from a configuration map entry. It should not require a full settings load that validates the cryptographic secret.

## Expected Behavior

- A dedicated, lightweight check for whether in-cluster access is enabled should be available that reads directly from the configuration map, without touching the main secret.
- This check should default to enabled when the configuration map does not explicitly disable it.
- When the configuration map itself cannot be found, the check should still return the default value (enabled) along with an informational error.
- All cluster operations (registering, listing, watching, name resolution) must succeed when the main secret is missing its cryptographic key, provided in-cluster access is not explicitly disabled.
- If an administrator has explicitly disabled in-cluster access in the configuration map, attempting to register an in-cluster cluster must still be rejected with a clear error.

## Why This Matters

Operators and automated provisioning tools should not be blocked from managing clusters simply because the system secret hasn't been fully initialized yet. The in-cluster access check is entirely independent of the cryptographic key and should be treated as such.
