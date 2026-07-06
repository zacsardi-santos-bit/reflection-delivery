## Description

When looking up which server URL corresponds to a given cluster name, Argo CD currently checks whether the special local (in-cluster) mode is enabled for **every** name lookup — even for completely normal, externally-defined clusters. This check requires reading from a configuration resource. If that resource is absent or unavailable (which can happen in certain deployment configurations), the cluster name lookup fails entirely, even though the in-cluster check is irrelevant for external cluster names.

The fix should make this check lazy: it should only be performed when the caller is specifically looking up the reserved local cluster by its special name. For all other cluster names, the check should be skipped entirely.

## Expected Behavior

- Looking up an external cluster by name must succeed even when the in-cluster configuration resource is absent.
- Looking up the reserved in-cluster name must still invoke the in-cluster enablement check (and propagate any resulting errors).
- When counting applications assigned to a specific cluster, apps that target the cluster by name should be counted only when that name unambiguously identifies a single cluster. If a name is shared by multiple cluster definitions, apps using that name should not be attributed to any cluster.
- A helper function should be introduced to resolve a destination's cluster server URL without fetching the full cluster object — for server-based destinations it returns the URL directly, for name-based destinations it calls the name-to-server lookup.

## Why This Matters

Unnecessary eager evaluation of the in-cluster check causes spurious lookup failures in environments where the in-cluster feature is simply not configured. This is a correctness and reliability issue that affects any workflow that resolves cluster names at runtime.
