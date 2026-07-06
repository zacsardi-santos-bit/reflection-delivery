## Description

The operator's resource-apply package provides a set of helper functions that controllers use to idempotently manage Kubernetes resources. These helpers encapsulate common reconciliation logic: computing and storing a hash of the desired state, comparing it against what is already in the cluster, verifying ownership before modifying resources, and emitting appropriate audit events. This pattern is already implemented for several resource types, but two important networking resource types — legacy endpoint records and modern endpoint slice records — are missing dedicated helpers.

Without these helpers, any controller that needs to manage endpoint-related resources must implement its own reconciliation logic from scratch, increasing the risk of inconsistency and subtle bugs (e.g., not checking controller ownership before updating, not correctly preserving externally added labels, or not being idempotent across multiple reconcile calls).

## Expected Behavior

- A new helper function for endpoint records must create, update, or leave unchanged an endpoint resource according to the desired state, respecting controller ownership semantics, hash-based change detection, and admission-webhook tolerance.
- A new helper function for endpoint slice records must provide the same behavior for the newer endpoint slice resource type.
- Both functions must emit appropriate informational events on create/update and warning events on failure.
- Both functions must be idempotent: repeated calls with unchanged desired state must produce no change and no events.
- Both functions must refuse to modify resources they do not own, and must handle the case where the lister cache and the API server are out of sync.

## Why This Matters

Controllers that manage networking endpoints in the operator need a consistent, safe way to apply the desired endpoint state. Having dedicated helpers that follow the same patterns as the rest of the resource-apply package ensures correctness and maintainability across all controllers that manage these resource types.
