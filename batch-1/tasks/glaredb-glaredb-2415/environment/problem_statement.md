## Description

When a user drops a table, GlareDB removes the table from its catalog (metadata store) but leaves the underlying data files on disk. This means that storage space is never actually reclaimed when tables are dropped — the data files accumulate indefinitely at the storage location.

## Expected Behavior

- When a table is dropped, its physical data files stored on disk should also be deleted
- The storage directory for the dropped table should be empty of files after the drop completes
- The catalog should be updated first (table removed from metadata), and then the physical file cleanup should happen
- Storage space should be properly freed when users drop tables

## Current Behavior

Dropping a table removes it from the catalog but leaves all of its data files on disk. The files remain in the storage directory even though the table no longer exists in the system.

## Why This Matters

Without this fix, any application that creates and drops tables regularly will continuously leak disk space. Users have no way to reclaim storage without manually deleting files from the storage backend. This is especially problematic for long-running deployments where tables are frequently created and dropped.

## Additional Note

The method for executing SQL statements in a local session should be made publicly accessible so that it can be exercised from integration tests that verify end-to-end behaviors like this one.
