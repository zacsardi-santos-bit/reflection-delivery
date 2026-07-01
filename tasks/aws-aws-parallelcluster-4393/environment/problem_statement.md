## Description

When updating a cluster, the system needs to distinguish between placement groups that are managed by the cluster itself (created automatically) and those that are provided explicitly by the user. Currently, all placement group changes go through the same update policy, which doesn't account for the special constraints around managed placement groups.

Specifically, when a cluster update would result in the deletion of a managed placement group — one that the cluster created automatically (no explicit user-provided name or identifier) — the system should require that all compute nodes be stopped first. Without this, it is possible to delete a managed placement group while compute capacity is still running, which can cause infrastructure problems.

## Expected Behavior

- The cluster update system should detect when a proposed config change would delete a managed placement group (a placement group that is enabled and has no user-specified name or identifier).
- If a managed placement group deletion is detected and the cluster still has running compute capacity, the update should be blocked with an appropriate message requiring that the entire compute fleet be shut down before the deletion can proceed.
- If the compute fleet is already stopped, the managed placement group deletion should be allowed to proceed.
- Placement groups that have an explicit user-provided name or identifier should not be subject to this stricter requirement — they follow the standard update strategy.
- The detection logic should work correctly for placement groups configured at both the queue level and the compute resource level, with the compute-resource-level configuration taking precedence over the queue-level configuration when both are present.

## Why This Matters

Without this guard, operators updating cluster configurations could inadvertently delete a managed placement group while nodes are still using it, leading to resource inconsistencies and potential cluster failures. This change makes the update system aware of managed placement group lifecycle requirements.
