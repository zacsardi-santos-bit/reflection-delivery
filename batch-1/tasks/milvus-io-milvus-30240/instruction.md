Implement a new sync policy for the data node's write buffer that introduces an intermediate "sealed" state for segments during flush operations. Ensure segments are first transitioned to this state before moving to "flushing," and update the default sync configuration to use this new policy.

*   Implement the `GetSealedSegmentsPolicy` function in the `internal/datanode/writebuffer/sync_policy.go` file.
    *   Accept a `MetaCache` parameter.
    *   Return a `SyncPolicy` that handles segments in the "Sealed" state.
*   Ensure the policy returned by `GetSealedSegmentsPolicy`:
    *   Calls `GetSegmentIDsBy` on the `MetaCache` to retrieve segment IDs using the "Sealed" state filter.
    *   Calls `UpdateSegments` on the `MetaCache` with an update function and two filter options to transition segments from "Sealed" to "Flushing" state.
    *   Returns the segment IDs collected from the "Sealed" state.
*   Replace the existing `GetFlushingSegmentsPolicy` with `GetSealedSegmentsPolicy` in the write buffer's default sync policies list.
*   Update the write buffer's auto-sync behavior, including the storage v2 variant, to use `GetSealedSegmentsPolicy` instead of `GetFlushingSegmentsPolicy`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.