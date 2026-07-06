## Description

There is a data-corruption bug in the record layer's handling of uncommitted versionstamp data during bulk deletion operations. When records are saved within a transaction, the system writes uncommitted versionstamp placeholders into a transaction-local buffer that are resolved at commit time. If a bulk deletion then occurs within the same transaction—such as deleting all records, deleting records matching a filter, deleting an entire store, disabling a version index, or removing a version index—these pending uncommitted mutations are not cancelled. As a result, the mutations are committed to the database even though the records themselves were deleted, leaving stale or incorrect version-tracking data in the store.

## Expected Behavior

- Deleting all records should clear both committed and uncommitted version data, so that after the operation no version information remains for any record key
- Deleting records by a prefix filter should remove version data (including uncommitted versionstamp mutations) for all matching records while leaving other groups intact
- Deleting an entire store should clear all version data for the deleted store without affecting other stores in the same transaction
- Disabling or removing a version index should cancel any pending uncommitted versionstamp mutations for that index, leaving the underlying storage range empty while preserving version data for records through other means
- Aggregate version indexes (such as those tracking the maximum version per group) must be properly cleaned up for deleted groups

## Why This Matters

If uncommitted versionstamp mutations are not properly cancelled during bulk deletions, subsequent version index scans and version lookups will return stale or incorrect data, silently corrupting the integrity of the record store. This can lead to hard-to-diagnose issues where deleted records appear to have version history in the database.
