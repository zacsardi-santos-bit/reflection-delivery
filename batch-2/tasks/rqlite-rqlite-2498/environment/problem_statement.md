## Description

When rqlite performs incremental snapshots, it collects WAL (Write-Ahead Log) files and moves them into the snapshot. Currently there is no integrity check on those WAL files before they are committed — a corrupt or missing file could silently pollute a snapshot, leading to data loss or difficult-to-diagnose failures later.

## Expected Behavior

- Each WAL file staged for an incremental snapshot should have a corresponding checksum file alongside it.
- Before committing the snapshot, the system should verify that the actual checksum of each WAL file matches the stored checksum. If no checksum file exists, or if the checksum does not match, the snapshot commit should fail with an error.
- A utility function should be available that compares a file's computed checksum against a stored checksum file, returning a clear true/false result (or an error if the files cannot be read).
- The function that writes checksum files should support an option to ensure the checksum file is fully flushed to disk after being written.

## Why This Matters

Without this integrity check, the snapshotting process has no way to detect a WAL file that was written incorrectly or became corrupt before the snapshot was finalized. Adding checksum verification at commit time ensures that only verified, intact WAL data makes it into snapshots, improving overall reliability.
