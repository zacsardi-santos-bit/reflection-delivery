## Description

When rolling back uncommitted changes in Merge-on-Read (MOR) tables, the rollback mechanism handles log files differently depending on the table version. In older table versions, log files must be preserved and a single rollback marker block should be written per file group to invalidate the uncommitted data. In newer table versions, each log file should be independently targeted for deletion.

The current implementation does not correctly handle the case where multiple log files from the same file group appear across separate rollback requests during marker-based rollback. Specifically:

- For older table versions: multiple rollback requests for the same file group are not properly consolidated, potentially resulting in incorrect rollback behavior instead of writing a single rollback block per file group.
- For newer table versions: log files are not correctly treated as individually deletable per request, which leads to an incorrect number of rollback requests being generated.

## Expected Behavior

- A utility method should be available to group rollback requests by file group, consolidating multiple log-only requests for the same file group into a single request with a merged set of log files to handle.
- The marker-based rollback strategy should produce one rollback request per log file for newer table versions, and one grouped request per file group for older table versions.
- The rollback execution should delete individual log files for newer tables and append a single rollback command block per file group for older tables.
- Null partition paths in rollback requests should be normalized to empty strings during grouping.

## Why This Matters

Without this fix, rolling back a large number of log file writes in a MOR table (a common operation when an inflight delta commit needs to be abandoned) can produce incorrect rollback results — either missing some files or creating redundant rollback blocks — leading to data inconsistency.
