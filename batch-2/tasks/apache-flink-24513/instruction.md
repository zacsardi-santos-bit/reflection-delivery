Implement a fix for the file-merging checkpointing feature in Flink to ensure that stateless operators are properly tracked in the checkpoint state lifecycle. Move certain state handle classes to the correct package, and ensure that even operators with no state have their managed directories registered and maintained correctly.

*   Move `SegmentFileStateHandle` from `org.apache.flink.runtime.checkpoint.filemerging` to `org.apache.flink.runtime.state.filemerging`.
    *   Update all import references in both tests and production code to reflect the new package path.

*   Create `DirectoryStreamStateHandle` in `org.apache.flink.runtime.state.filemerging`.
    *   Extend `DirectoryStateHandle` and implement `StreamStateHandle`.
    *   Provide a static factory method `forPathWithZeroSize(java.nio.file.Path)`.
    *   Implement `createStateRegistryKey()` to return a `SharedStateRegistryKey` based on the directory URI.

*   Create `FileMergingOperatorStreamStateHandle` in `org.apache.flink.runtime.state.filemerging`.
    *   Extend `OperatorStreamStateHandle` and implement `CompositeStateHandle`.
    *   Constructor must accept a task-owned `DirectoryStreamStateHandle`, a shared `DirectoryStreamStateHandle`, a state-name-to-offset map, and a delegate `StreamStateHandle`.
    *   Implement `registerSharedStates` to register all three handles with the `SharedStateRegistry`.

*   Create `EmptyFileMergingOperatorStreamStateHandle` in `org.apache.flink.runtime.state.filemerging`.
    *   Extend `FileMergingOperatorStreamStateHandle`.
    *   Provide a static `create` method with `DirectoryStreamStateHandle taskownedDirHandle` and `DirectoryStreamStateHandle sharedDirHandle`.

*   Create `EmptySegmentFileStateHandle` in `org.apache.flink.runtime.state.filemerging`.
    *   Extend `SegmentFileStateHandle`.
    *   Provide a public static `INSTANCE` singleton.
    *   Ensure `openInputStream()` throws an exception.

*   Update `FileMergingSnapshotManager` interface with `getManagedDirStateHandle`.
    *   Method signature: `DirectoryStreamStateHandle getManagedDirStateHandle(SubtaskKey subtaskKey, CheckpointedStateScope scope)`.

*   Implement `getManagedDirStateHandle` in `FileMergingSnapshotManagerBase`.
    *   Store `DirectoryStreamStateHandle` instances during `initFileSystem` (for EXCLUSIVE) and `registerSubtaskForSharedStates` (for SHARED).
    *   Return handles by scope.

*   Add methods to `FsMergingCheckpointStorageLocation`.
    *   `getExclusiveStateHandle()` and `getSharedStateHandle()` should delegate to the snapshot manager's `getManagedDirStateHandle`.

*   Ensure snapshot results for operators with no registered state contain a non-null `FileMergingOperatorStreamStateHandle` when using `FsMergingCheckpointStorageLocation`.

*   Ensure directories registered by `FileMergingOperatorStreamStateHandle` persist on the filesystem until no longer referenced by any active checkpoint.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.