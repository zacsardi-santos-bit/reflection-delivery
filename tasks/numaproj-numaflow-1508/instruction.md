Refactor the storage layer of the reduce pipeline's data-forwarding component to improve composability and interface ergonomics. Ensure the store manager is passed as a separate dependency, update replay and discovery interfaces, and adjust write functionality to include persistence control.

*   Update the `NewDataForward` function:
    *   Accept `storeManager` as a separate parameter between `pbqManager` and `whereToDecider`.
    *   Ensure `pbqManager` and `storeManager` are created independently and passed to `NewDataForward`.

*   Modify the `Write` method in `pkg/reduce/pbq/pbq.go`:
    *   Add a boolean parameter `persist` to control message persistence.
    *   Ensure the method signature is `Write(ctx context.Context, request *window.TimedWindowRequest, persist bool) error`.

*   Revise the `store.Manager` interface:
    *   Replace `DiscoverPartitions` with `DiscoverStores(ctx context.Context) ([]store.Store, error)`.
    *   Ensure `DiscoverStores` returns store instances directly, not partition IDs.

*   Update the `store.Store` interface:
    *   Replace `Read(int64)` with `Replay() (<-chan *isb.ReadMessage, <-chan error)`.
    *   Ensure `Replay` streams messages through channels and closes them upon completion.

*   Implement the filesystem-backed WAL store manager:
    *   Create `NewFSManager` in `pkg/reduce/pbq/store/aligned/fs/manager.go`.
    *   Ensure it replaces the old `NewWALStores` constructor from the `wal` package.

*   Develop `NewWriteOnlyWAL` function in `pkg/reduce/pbq/store/aligned/fs/wal_segment.go`:
    *   Accept parameters: `partitionID`, `fName`, `maxBufferSize`, `syncDuration`, `pipelineName`, `vertexName`, `replicaIndex`.
    *   Return `(store.Store, error)` with a concrete type of `*WAL`.

*   Adjust the `WAL` struct in `pkg/reduce/pbq/store/aligned/fs/wal_segment.go`:
    *   Include fields: `maxBatchSize`, `syncDuration`, `readUpTo`, `rOffset`, `partitionID`, `prevSyncedWOffset`, `prevSyncedTime`.
    *   Ensure fields are directly accessible and not nested.

*   Define the `fsWAL` struct in `pkg/reduce/pbq/store/aligned/fs/manager.go`:
    *   Include fields: `storePath`, `maxBatchSize`, `syncDuration`, `pipelineName`, `vertexName`, `replicaIndex`, `activeStores`.
    *   Initialize `activeStores` as a map.

*   Ensure `DiscoverStores` in the filesystem manager scans for store segments and returns `store.Store` instances that support `Replay`.

*   Remove the `Replay` method from `pbq.Manager`; handle replay directly through `store.Store`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.