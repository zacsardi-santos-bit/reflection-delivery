## Description

The arrow-select library contains an internal mechanism for coalescing multiple small record batches into larger, more efficient ones. This is especially useful after filtering or selection operations that produce many small output batches. However, this functionality is currently private and cannot be used by consumers of the library — trying to reference it from external code fails to compile.

## Expected Behavior

- The batch coalescing type should be publicly accessible from outside the arrow-select crate.
- Users should be able to create a new coalescer by specifying a schema and a target output batch size.
- Users should be able to incrementally push record batches into the coalescer.
- Users should be able to signal that buffering is complete and have the remaining in-progress data finalized.
- Users should be able to retrieve completed batches one at a time, with each completed batch containing the expected number of rows.

## Why This Matters

Applications that perform operations producing small, fragmented output batches (such as filtered results) need a way to reassemble those fragments into appropriately sized batches for downstream processing. This feature already exists in the codebase but is not exposed to library consumers, forcing them to either re-implement it or work with inefficient small batches.
