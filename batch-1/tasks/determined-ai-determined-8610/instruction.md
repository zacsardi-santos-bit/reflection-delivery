Fix the message validation helper in the streaming system's test infrastructure to ensure reliable integration testing of real-time update scenarios. Correct the sorting logic for deletion messages and apply sorting only when necessary to prevent crashes or incorrect behavior.

*   Update the `validateMsgs` function in `master/internal/stream/test_util.go`:
    *   Guard each `sort.Slice` call with a length check to ensure sorting is performed only when the slice contains more than one element (`len > 1`).
    *   Correct the sort comparator for the `expectedDeletions` slice to use `expectedDeletions[i]` and `expectedDeletions[j]` for comparisons.

*   Ensure the streaming system handles the following scenarios correctly after the fix:
    *   Database updates affecting entities outside a client's subscription should result in no messages being sent to the subscriber.
    *   When a client reconnects after being offline, include upsert messages for entities that fall within its subscription and were added while it was offline.
    *   Include deletion messages listing IDs of entities that were deleted while the client was offline.
    *   Send an upsert message when an entity is created within an active subscription's scope.
    *   Send a deletion message when an entity moves out of an active subscription's scope.
    *   Send an upsert message when an entity moves into an active subscription's scope.
    *   Send a deletion message when an entity within an active subscription's scope is deleted from the database.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.