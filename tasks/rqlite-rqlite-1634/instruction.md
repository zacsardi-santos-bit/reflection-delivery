Implement a mechanism to handle failed WAL checkpoints during snapshot operations in a distributed SQLite database. Ensure that after a failure, the system marks the need for a full snapshot on the next attempt.

*   Update the `Snapshot` method in `store/store.go`:
    *   Ensure it returns a non-nil error when a WAL checkpoint fails during the snapshot process.
    *   Call `SetFullNeeded` on the snapshot store to record that the next snapshot must be a full snapshot.
    *   Method signature: `Snapshot(minSnapshots uint64) error`.

*   Implement the `SetFullNeeded` method in `store/snapshot_store.go` (or similar):
    *   Ensure it marks the snapshot store to indicate that the next snapshot must be a full snapshot.
    *   Method signature: `SetFullNeeded() error`.

*   Implement the `FullNeeded` method in `store/snapshot_store.go` (or similar):
    *   Ensure it returns `(true, nil)` after `SetFullNeeded` has been called, indicating a full snapshot is required.
    *   Ensure it returns `(false, nil)` after a successful snapshot with no checkpoint failure.
    *   Method signature: `FullNeeded() (bool, error)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.