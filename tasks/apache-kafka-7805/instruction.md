Implement functionality in the `MetadataResponse` class to handle leader epoch reliability during partition reassignment. Ensure the client correctly identifies and processes metadata responses based on their reliability to prevent blocking during operations.

*   Implement the `hasReliableLeaderEpochs()` method in `MetadataResponse`:
    *   Return `true` when the response is constructed from `MetadataResponseData`.
    *   Return `false` when constructed from `Struct` with protocol version below 9.
    *   Return `true` for protocol version 9 and above when constructed from `Struct`.

*   Update metadata handling logic:
    *   When `hasReliableLeaderEpochs()` returns `false`, store the leader epoch as `-1` (NO_PARTITION_LEADER_EPOCH).
    *   When `hasReliableLeaderEpochs()` returns `true`, store the actual epoch value from the response.
    *   Ignore metadata updates with a lower leader epoch than what is cached, retaining the existing `PartitionInfoAndEpoch`.

*   Ensure `lastSeenLeaderEpoch(TopicPartition)`:
    *   Continues to return the higher cached epoch after rejecting a stale update.

*   Ensure `partitionInfoIfCurrent(TopicPartition)`:
    *   Returns a non-empty `Optional` containing the current `PartitionInfoAndEpoch` after a successful metadata update.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.