## Description

Several edge cases in the live VM migration lifecycle are not handled correctly, leading to incorrect state transitions, lost timing data, and incorrect cleanup behavior for migration target instances.

## Expected Behavior

- When a migration fails, the system should record the failure end time while preserving any previously recorded start time. Currently, the start timestamp can be overwritten or lost during failure handling.

- For virtual machines created specifically as migration targets on the destination node, cleanup should be context-aware: if the migration failed, the VM should retain a marker indicating it was created as a migration target (so it can be handled appropriately); if the migration succeeded, all migration-related markers should be fully removed.

- When establishing a migration proxy connection, the system needs to correctly determine which identity (source or target) to use as the lookup key. For decentralized migrations using a UNIX-socket transport, the source VM's identity should be used; for all other cases, the local VM's identity should be used.

- The migration transport configuration should be included in the status information synchronized from source to target nodes.

- Migration target VMs that are in a transitional "scheduled" state with a failed or terminated pod, or whose underlying migration has already been marked as failed, should move to a stable waiting state — rather than being treated as crashed non-target VMs.

## Why This Matters

These issues can cause cascading failures: a migration target VM may incorrectly transition to a Failed state, migration history data gets corrupted by overwriting timestamps, cleanup leaves behind stale annotations that interfere with future reconciliation, or proxy connections are established using the wrong identity causing the migration to hang or fail. Fixing these edge cases improves the overall reliability and observability of VM live migration.
