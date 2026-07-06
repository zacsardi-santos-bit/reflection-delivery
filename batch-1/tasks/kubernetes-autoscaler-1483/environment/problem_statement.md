## Description

The cluster autoscaler for GCE currently retrieves nodes in a managed instance group as a flat list of instance ID strings. This means the autoscaler has no visibility into the actual state of each node — it cannot tell whether a node is actively running, still being provisioned, or in the process of being deleted.

More critically, when provisioning fails (for example, due to resource quota exhaustion or regional resource stockouts), the autoscaler has no way to report this information upstream. These are important signals that could allow higher-level components to react appropriately — for example, by avoiding a particular zone, reducing scale-up aggressiveness, or surfacing an actionable error to the user.

## Expected Behavior

- When querying the nodes of a managed instance group, each node should be returned with both its identifier and its current lifecycle state (running, being created, or being deleted).
- For nodes that are actively being created and have encountered errors, the response should include structured error information: an error category (resource shortage vs. other), a normalized error code, and a human-readable error message.
- Errors during deletion should not be surfaced — only creation errors are relevant.
- When multiple errors are associated with a single instance, resource-shortage errors should take precedence in the error category and code, while the error messages from all errors should be combined.

## Why This Matters

Without this information, the autoscaler cannot distinguish transient creation failures from stable running nodes, and it has no mechanism to propagate provisioning error details to users or downstream systems. Surfacing this state enables smarter scaling decisions and better observability into why a node group may be failing to scale up.
