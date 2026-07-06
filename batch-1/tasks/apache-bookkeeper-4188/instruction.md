Implement a batch read capability in the BookKeeper client to allow reading multiple ledger entries from a bookie in a single network request. Define and implement the necessary methods and interfaces to support this functionality, ensuring efficient handling of consecutive entries and proper error reporting.

*   Update the `BookieClient` interface:
    *   Declare an abstract method `batchReadEntries` with the following signature:
        *   `void batchReadEntries(BookieId addr, long ledgerId, long startEntryId, int maxCount, long maxSize, BookkeeperInternalCallbacks.BatchedReadEntryCallback cb, Object ctx, int flags, byte[] masterKey, boolean allowFastFail)`
    *   Provide a default method overload for `batchReadEntries` without `masterKey` and `allowFastFail`:
        *   `default void batchReadEntries(BookieId addr, long ledgerId, long startEntryId, int maxCount, long maxSize, BookkeeperInternalCallbacks.BatchedReadEntryCallback cb, Object ctx, int flags)`
        *   Ensure this overload delegates to the full 10-parameter version.

*   Define the `BatchedReadEntryCallback` interface in `BookkeeperInternalCallbacks`:
    *   Include the method `void readEntriesComplete(int rc, long ledgerId, long startEntryId, ByteBufList bufList, Object ctx)`.

*   Implement the `batchReadEntries` method in `BookieClientImpl`:
    *   Ensure it sends a batch read request to the bookie server asynchronously.
    *   Invoke the `BatchedReadEntryCallback` upon completion of the request.

*   Ensure the batch read operation:
    *   Returns up to `maxCount` consecutive entries starting from `startEntryId`.
    *   Stops at the first gap, the count limit, or when the cumulative frame size exceeds `maxSize`.
    *   Invokes the callback with `BKException.Code.OK` and a `ByteBufList` containing the entries.
    *   Handles each entry buffer to include: ledgerId, entryId, last-add-confirmed, cumulative length, digest, and payload in the specified order.
    *   Completes with `Code.NoSuchEntryException` if the starting entry is missing, returning an empty `ByteBufList`.
    *   Accounts for response framing overhead in the size limit calculation, starting at 36 bytes and adding `entry.readableBytes()+4` for each entry.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.