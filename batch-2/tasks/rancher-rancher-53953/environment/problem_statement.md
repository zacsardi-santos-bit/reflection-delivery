## Description

The RBAC aggregation feature in Rancher relies on labeled Kubernetes resources (role bindings and cluster roles) to track which objects it has created. Currently, both the management plane and the downstream user cluster controllers use the same label to mark their respective aggregation resources. This shared label makes it impossible to distinguish which layer of the system created and owns a particular resource.

This causes two related problems:

1. When the aggregation feature is disabled and a migration cleanup runs, the management-plane controller cannot reliably filter to only its own resources, and may accidentally target resources that belong to the downstream cluster side (or vice versa).
2. When the feature is turned off, the cleanup only removes management-plane role bindings but does not clean up corresponding resources in downstream clusters, leaving orphaned resources behind.

## Expected Behavior

- The management plane should use a distinct label (separate from the one used by downstream cluster controllers) to mark its aggregation-related role bindings and cluster roles.
- The downstream (user cluster) side should continue using its own label.
- Listing operations that find resources to reconcile or clean up should use combined label selectors — the resource owner identifier together with the appropriate aggregation label — to avoid operating on unrelated resources.
- When the aggregation feature flag is disabled, the migration process should clean up resources at both the management-plane level and in the corresponding downstream clusters.
- Helper functions should be available to apply the correct aggregation label in each context (management-plane and downstream).
- The function that builds aggregating cluster roles should not automatically apply an aggregation label; callers should apply the appropriate label separately.

## Why This Matters

Without distinct labels, disabling the aggregation feature can leave orphaned role bindings in downstream clusters, or cause the wrong layer to accidentally clean up resources it does not own. Clearer label separation ensures each controller operates only on its own resources and that full cleanup is performed when the feature is toggled off.
