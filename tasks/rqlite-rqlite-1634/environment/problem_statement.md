## Description

When the system takes an incremental snapshot and the write-ahead log (WAL) checkpoint step fails — for example because the SQLite database is locked by another active connection — the snapshot returns an error but takes no further recovery action. The next snapshot attempt will again try an incremental approach, potentially encountering the same issue, and leaving replicas in an uncertain state.

## Expected Behavior

- A failed WAL checkpoint during snapshotting should cause the snapshot operation to return an error.
- In addition to returning an error, the system should automatically record that the next snapshot must be a complete, full-database snapshot rather than an incremental one.
- Subsequent calls to check whether a full snapshot is needed should confirm that the flag was set.

## Why This Matters

Incremental snapshots depend on a consistent sequence of WAL files. If a checkpoint fails partway through, the main database file and the WAL are out of sync, so the next incremental snapshot would be built on a broken foundation. Forcing a full snapshot on the next attempt ensures that the complete database state is captured, guaranteeing consistency for all replicas and preventing cascading snapshot failures.
