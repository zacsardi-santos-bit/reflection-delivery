Implement a function to automatically migrate old-format snapshots to a new generation-based format when a node starts. Ensure the migration handles various scenarios gracefully, such as non-existent or empty directories, and properly updates the snapshot storage.

*   Implement the `Upgrade` function in `snapshot/upgrader.go` with the signature:
    ```go
    Upgrade(old string, new string, logger *log.Logger) error
    ```
    *   Accepts paths to the old and new snapshot directories and a logger instance.
    *   If the old directory does not exist or is empty, return `nil` without performing any actions.
    *   Convert the most recent v7.x-format snapshot to the new format in the new directory.
    *   After a successful migration, remove the old snapshot directory to prevent reprocessing.

*   Ensure that after a successful upgrade:
    *   A `SnapshotStore` created at the new directory with `NewStore` must return a current generation directory with the base name `firstGeneration` ("0000000001") when `GetCurrentGenerationDir` is called.
    *   `List` must return exactly one snapshot entry.
    *   The snapshot returned by `List` must have an ID matching the most recent old-format snapshot.
    *   `Open` must return metadata with an ID matching the requested snapshot ID.

*   Verify that a node started with v7.20.3 data can become a Raft leader and correctly process queries, such as a `COUNT(*)` query returning a row count of 20.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.