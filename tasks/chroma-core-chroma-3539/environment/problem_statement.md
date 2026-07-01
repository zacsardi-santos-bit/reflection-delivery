## Description

The system maintains a history of collection versions, but currently lacks the infrastructure to safely garbage-collect old versions. A safe GC process requires two distinct phases: first, earmark the versions that should be deleted (so that GC knows what to clean up without immediately removing them), and second, confirm the removal by permanently dropping those entries from the history once the associated data has been cleaned up.

Neither phase is currently implemented. Without the marking phase, the GC cannot record intent durably before it begins cleaning. Without the removal phase, the version history accumulates stale entries indefinitely.

## Expected Behavior

- It should be possible to mark a set of specific versions within a collection's version history as "pending deletion." After this operation, the version history file should still contain all versions, but the specified ones should be flagged accordingly.
- It should be possible to permanently remove a set of specific versions from a collection's version history file. After this operation, the version history should contain only the non-deleted versions.
- Both operations should accept multiple collections in a single request and return a per-collection success indicator.
- If a requested collection does not exist, the operation should report failure for that collection without returning an overall error.
- If versions requested for marking do not exist in the version history, the operation should report failure for that collection without returning an overall error.
- A helper should be available to look up the current version tracking file name for a given collection.
- The object storage interface must support deleting a specific version file by name.
- The database interface must support atomically swapping the version file name for a collection, succeeding only if the current stored name matches the expected value.

## Why This Matters

Without these operations, the garbage collection system has no durable way to track which versions are scheduled for removal or to clean up the version history after GC completes. This leads to unbounded growth in version metadata and prevents safe, incremental GC of old collection snapshots.
