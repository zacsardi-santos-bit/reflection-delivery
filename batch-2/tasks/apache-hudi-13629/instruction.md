Implement a cleanup mechanism for Hudi's metadata table reader to manage memory leaks caused by uncached distributed data collections. Create a manager to track and release these collections on exceptions, ensuring thread safety and proper resource management.

*   Create `HoodieDataCleanupManager` class in `org.apache.hudi.metadata` package:
    *   Implement a default no-argument constructor.
    *   Provide `void trackPersistedData(HoodiePairData<?, ?> data)` method to track `HoodiePairData`.
    *   Provide `void trackPersistedData(HoodieData<?> data)` method to track `HoodieData`.
    *   Implement `<T> T ensureDataCleanupOnException(SerializableFunctionUnchecked<Void, T> operation)` method:
        *   Execute the operation and return its result on success.
        *   On exception, call `unpersistWithDependencies()` on all tracked objects for the current thread, clear the tracking list, and re-throw the exception.
    *   Implement `ConcurrentHashMap<Long, List<Object>> getThreadPersistedData()` method with package-accessible visibility for testing.

*   Ensure thread isolation in `HoodieDataCleanupManager`:
    *   Track data objects per-thread using the thread ID.
    *   Ensure cleanup in one thread does not affect another thread's tracked data.
    *   Continue cleanup for remaining objects if `unpersistWithDependencies()` throws during exception-triggered cleanup.
    *   Silently skip null entries and objects not of type `HoodieData` or `HoodiePairData`.

*   Update `HoodieData<T>` interface:
    *   Add `void unpersistWithDependencies()` method to recursively unpersist the data and its upstream dependencies.

*   Update `HoodiePairData<K, V>` interface:
    *   Add `void unpersistWithDependencies()` method with the same recursive unpersist semantics as `HoodieData`.

*   Modify `HoodieBackedTableMetadata` class:
    *   Add a private final field `HoodieDataCleanupManager dataCleanupManager` initialized to `new HoodieDataCleanupManager()`.
    *   Wrap core logic of the following methods in `dataCleanupManager.ensureDataCleanupOnException()`:
        *   `readRecordIndex(HoodieData<String> recordKeys)`
        *   `readRecordIndexLocations(HoodieData<String> recordKeys)`
        *   `readSecondaryIndex(HoodieData<String> secondaryKeys, String partitionName)`
        *   `readSecondaryIndexLocations(HoodieData<String> secondaryKeys, String partitionName)`
    *   Ensure exceptions from `ensureDataCleanupOnException` propagate unchanged to the caller.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.