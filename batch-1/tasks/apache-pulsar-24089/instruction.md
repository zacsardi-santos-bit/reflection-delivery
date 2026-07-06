Implement the `EntryCountEstimator` utility class to estimate the number of ledger entries that can be read within a specified byte budget. Extract the logic from the cursor implementation to make it independently testable and ensure it handles read-only cursors without crashing.

*   Create the `EntryCountEstimator` class in `org.apache.bookkeeper.mledger.impl`:
    *   Ensure the class is non-instantiable with only static methods.
    *   Implement the static method `estimateEntryCountByBytesSize(int maxEntries, long maxSizeBytes, Position readPosition, ManagedLedgerImpl ml)` to delegate to `internalEstimateEntryCountByBytesSize`.
    *   Implement `internalEstimateEntryCountByBytesSize(int maxEntries, long maxSizeBytes, Position readPosition, NavigableMap<Long, MLDataFormats.ManagedLedgerInfo.LedgerInfo> ledgersInfo, Long lastLedgerId, long lastLedgerTotalEntries, long lastLedgerTotalSize)`.

*   Handle specific cases in `internalEstimateEntryCountByBytesSize`:
    *   Return 0 if `maxSizeBytes` is 0 or negative.
    *   Return `maxEntries` immediately if `maxSizeBytes` equals `Long.MAX_VALUE`.
    *   Adjust the read position based on `readPosition` and `ledgersInfo`:
        *   Clamp to `(lastLedgerId, max(lastLedgerTotalEntries - 1, 0))` if `readPosition` is past the last ledger.
        *   Reset to `(firstKey, 0)` if `readPosition` is before the first ledger.
    *   Skip empty ledgers and use a default average size if necessary.
    *   Calculate per-entry average size and total ledger size including overhead.
    *   Determine entries to read based on the remaining byte budget and read position.
    *   Ensure the method always returns at least 1 and does not exceed `maxEntries`.

*   Modify `RangeEntryCacheImpl`:
    *   Change `DEFAULT_ESTIMATED_ENTRY_SIZE` to `public static final int` with a value of 10240.

*   Ensure the read-only cursor's asynchronous read operation:
    *   Completes successfully without a `NullPointerException` when the active ledger is null.
    *   Returns the correct number of entries, bounded by the entry count limit.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.