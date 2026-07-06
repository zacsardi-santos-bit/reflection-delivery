## Description

Snapshot garbage collection needs a way to safely determine whether a deleted key or directory entry can be reclaimed from storage. Currently there is no mechanism to atomically load and lock the relevant preceding snapshots in the chain while making this reclamation decision. Without such a mechanism, the garbage collector can race with snapshot additions or deletions, leading to incorrect reclamation decisions that could cause data loss or inconsistency.

## Expected Behavior

- A new reusable filter component should be available in the snapshot filter package that, given the current snapshot context and the number of previous snapshots to consider, can determine whether a key is reclaimable.
- When the filter evaluates a key, it must automatically load and acquire locks on the required previous snapshots in the chain.
- If the current snapshot's volume or bucket does not match the key being evaluated, the filter must raise an error with a clear message identifying the mismatch.
- If any required previous snapshot is no longer active (e.g., has been deleted), the filter must raise an error rather than proceeding with a potentially stale view.
- If any required previous snapshot has changes that have not yet been persisted to disk, the filter must raise an error to prevent premature reclamation.
- If the snapshot chain changes during processing (e.g., a new snapshot is added), the filter must detect this and re-initialize before completing the check.
- A new lock resource type must be introduced specifically for protecting snapshot garbage collection operations.

## Why This Matters

Snapshot-aware garbage collection must operate against a stable, consistent view of the snapshot chain. Without proper locking and validation of snapshot state, garbage collection could either reclaim data that is still referenced by an active snapshot, or fail silently when a snapshot becomes unavailable. This filter provides the foundation for safe, consistent reclaimability checking across all snapshot garbage collection paths.
