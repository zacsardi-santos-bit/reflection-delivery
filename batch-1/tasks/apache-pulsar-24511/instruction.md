Implement a reusable utility for replaying entries from a managed ledger cursor. This utility should read entries in batches, process them using a provided entry processor, and handle errors gracefully. It should return the position of the last successfully processed entry and track the number of processed entries.

Requirements:

*   Implement the `ManagedLedgerReplayTask` class in `managed-ledger/src/main/java/org/apache/bookkeeper/mledger/ManagedLedgerReplayTask.java`.
    *   Constructor: `ManagedLedgerReplayTask(String name, Executor executor, int maxEntriesPerRead)`
        *   Accepts a ledger name, an executor for running batches, and a maximum number of entries to read per batch.
    *   Method: `replay(ManagedCursor cursor, EntryProcessor processor) -> CompletableFuture<Optional<Position>>`
        *   Accepts a `ManagedCursor` and an `EntryProcessor`.
        *   Returns a `CompletableFuture<Optional<Position>>`.
        *   Completes with an empty `Optional` if the cursor has no entries.
        *   Reads entries in batches of at most `maxEntriesPerRead`.
        *   Invokes the executor for each batch.
        *   Returns the position of the last successfully processed entry in the `Optional`.
        *   Stops processing if the `EntryProcessor` throws an exception, returning the last successful position.
        *   Propagates unexpected cursor operation exceptions through the future.
    *   Method: `getNumEntriesProcessed() -> int`
        *   Returns the number of entries successfully processed during the most recent replay call.
        *   Resets the counter to 0 with each call to `replay`.

*   Implement the `EntryProcessor` interface in `managed-ledger/src/main/java/org/apache/bookkeeper/mledger/EntryProcessor.java`.
    *   Method: `process(Position position, ByteBuf buffer) -> void`
        *   Processes individual entries during replay.
        *   Receives the position and data buffer for each entry.

*   Ensure that entry buffers are released correctly after processing to prevent memory leaks.
*   When there are no entries in the cursor, ensure the result clearly indicates that nothing was processed.
*   Handle partial failures by stopping processing at the last successful entry if the processor rejects an entry.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.