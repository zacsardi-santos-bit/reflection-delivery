## Description

When resolving which cluster an application targets by its friendly name, Argo CD unnecessarily checks whether local in-cluster deployment mode is enabled — even when the target cluster has nothing to do with local in-cluster access. This check requires the ArgoCD configuration resource to be present and readable. If that resource is missing or unavailable, the check fails, causing **every** cluster-by-name lookup to fail, regardless of which cluster is actually being requested.

## Expected Behavior

- Looking up a cluster by name should only trigger the in-cluster-enabled check when the name being resolved is the special identifier for the local cluster.
- For all other cluster names, the lookup should proceed without touching the in-cluster configuration.
- When the in-cluster-enabled check is required and the configuration resource is absent, the error must be surfaced to the caller rather than swallowed or logged as a warning.

## Additional: Application Counting and Ambiguous Names

The logic that tracks how many applications are deployed to each cluster should handle the case where multiple clusters share the same name. If a cluster name is ambiguous (it maps to more than one server address), applications that reference the cluster by that name should not be counted toward any single cluster's total — counting them would produce incorrect attribution.

## Why This Matters

Operators who manage many clusters where the ArgoCD configuration resource may occasionally be unavailable should not see spurious failures when resolving ordinary named clusters. This fragility makes the system less reliable than it should be, and the fix improves resilience by scoping the in-cluster check to only the situations where it is actually needed.
