Implement a system to track and report the size of state restored from different storage locations during a Flink task's checkpoint recovery. Ensure each state handle can categorize its size by storage location and aggregate this information into task initialization metrics. Additionally, create a utility to compute the total size of files within a directory.

Requirements:

*   Update `flink-core`:
    *   Add `FileUtils.getDirectoryFilesSize(java.nio.file.Path directory)` to sum file sizes in a directory recursively, returning 0 for empty directories.

*   Update `StateObject` in `flink-runtime`:
    *   Define a nested enum `StateObjectLocation` with values: `LOCAL_DISK`, `LOCAL_MEMORY`, `REMOTE`, `UNKNOWN`.
    *   Define a static nested class `StateObjectSizeStatsCollector` with:
        *   `create()` to instantiate the collector.
        *   `add(StateObjectLocation key, long value)` to accumulate size by location.
        *   `getStats()` to return an `EnumMap<StateObjectLocation, Long>` of sizes.
    *   Declare `collectSizeStats(StateObjectSizeStatsCollector collector)` for state handle implementations to report size by location.

*   Update specific state handle classes to implement `collectSizeStats`:
    *   `ByteStreamStateHandle`: Report byte array length under `LOCAL_MEMORY`.
    *   `FileStateHandle`: Report size under `LOCAL_DISK` for 'file' or no URI scheme, `REMOTE` for other schemes.
    *   `KeyGroupsStateHandle`: Report size under `LOCAL_MEMORY`.
    *   `IncrementalRemoteKeyedStateHandle`: Report size based on underlying storage, `LOCAL_MEMORY` for local in-memory file system.
    *   `IncrementalLocalKeyedStateHandle`: Report sizes under `LOCAL_DISK` and/or `LOCAL_MEMORY`, excluding `REMOTE` or `UNKNOWN`.

*   Update `DirectoryStateHandle` in `flink-runtime`:
    *   Provide `forPathWithSize(java.nio.file.Path directory)` to create a handle with pre-computed size using `FileUtils.getDirectoryFilesSize`.

*   Update `BackendRestorerProcedure` in `flink-streaming-java`:
    *   Modify `createAndRestore(Collection<S> restoreOptions, StateObject.StateObjectSizeStatsCollector stats)` to accept a collector and accumulate size statistics.

*   Update `MetricNames` in `flink-runtime`:
    *   Define `RESTORED_STATE_SIZE` as a prefix for restored state size metrics.

*   Update `RocksIncrementalSnapshotStrategy` in `flink-state-backends`:
    *   Remove `CloseableRegistry` parameter from the constructor and update all call sites accordingly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.