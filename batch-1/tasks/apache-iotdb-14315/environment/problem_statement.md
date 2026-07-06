## Description

The table-model deletion feature is missing several critical pieces that prevent it from working correctly end-to-end. The internal plan node used to represent a table-model delete operation is missing a database-name field, which causes problems when the same deletion must be forwarded to multiple storage regions or recovered after a server restart — there is no way to know which database the deletion belongs to.

Additionally, the modification file system (which records deletions to apply during reads) does not support the table-model deletion entry types. There is no way to batch-write multiple entries at once, and the compaction logic that merges redundant deletion records does not know how to handle table-model entries. As a result, modification files containing table-model deletions grow unboundedly and are never compacted.

Finally, there is no upgrade path for modification files written in the legacy format. Systems that stored deletions before the new format was introduced cannot read or process those records after upgrading.

## Expected Behavior

- The delete plan node must carry a database name field (which may be absent) and correctly preserve it through serialization and deserialization.
- The modification file must accept batch writes of multiple deletion entries at once.
- Table-model deletion entries with various predicate types (match all, match by segment, match by full device identity, or match by table name only) must be serializable to and recoverable from a modification file.
- Modification file compaction must merge table-model deletion entries that share the same table and predicate into a single entry when the file grows large enough (at or above 1 MB); below that threshold, compaction should not aggressively reduce the entries.
- When multiple distinct table-predicate groups exist, each group must compact independently.
- A legacy modification file upgrade mechanism must exist that converts old-format entries to the new format, runnable either synchronously or asynchronously.
- After an upgrade, the old file is removed, the new file exists, all entries are preserved, and the file remains writable.

## Why This Matters

Without these fixes, table-model deletions cannot be reliably stored, compacted, or recovered, making the feature incomplete and potentially causing data inconsistencies after restarts or upgrades.
