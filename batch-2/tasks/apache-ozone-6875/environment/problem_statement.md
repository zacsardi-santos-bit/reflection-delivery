## Description

When blocks are deleted from a storage container, there is currently no mechanism to record those deletions in the container's checksum tree file. This matters for container reconciliation: if two replicas of a container diverge, the reconciliation process needs to know which blocks have already been deleted so it doesn't attempt to copy them from another replica unnecessarily.

## Expected Behavior

- After block deletion runs, each affected container's checksum tree file should contain the list of block IDs that were deleted, in sorted order and without duplicates.
- If a deletion is retried (for example after a partial failure where physical block files were already removed but the metadata was not yet updated), the checksum tree file should still be updated correctly even though the block files are no longer present on disk.
- When the same block ID appears multiple times in a deletion request, it should be stored only once in the file.
- When deletion requests arrive in arbitrary order, the stored block list must always be sorted in ascending order.
- Multiple deletion batches for the same container must accumulate, so the file reflects the union of all previously deleted blocks.

## Why This Matters

Without this tracking, reconciliation between container replicas has no way to distinguish between a block that was legitimately deleted and one that is missing due to corruption or replication failure. Adding this tracking closes that gap and allows reconciliation to operate correctly.

## Additional Refactoring

Shared test helpers for building container checksum tree structures and reading checksum tree files from disk should be consolidated into a common utility class so different test suites can reuse them without duplication.
