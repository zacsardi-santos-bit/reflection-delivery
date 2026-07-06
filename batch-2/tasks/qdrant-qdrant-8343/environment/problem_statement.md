## Description

The duplicate-removal logic that runs during segment compaction doesn't correctly handle records that are in a "pending" (not-yet-indexed) state. When the same record appears in multiple segments with different versions, the cleaner currently picks the highest-versioned copy as the winner and removes all other copies — regardless of whether the winning copy is actually accessible through the index yet.

This causes a correctness problem: if the newest version of a record is still pending (not yet indexed), removing the older-but-fully-accessible copy leaves the record temporarily invisible to queries. The database should instead preserve the accessible copy until the pending version finishes being indexed.

## Expected Behavior

- A pending/deferred copy of a record should never be deleted during deduplication, no matter what version it has.
- When the newest version of a record exists only as a pending copy, all older fully-indexed copies must be preserved intact.
- When the newest version of a record has a fully-indexed copy, older fully-indexed copies can be removed as usual.
- If a record has multiple pending copies across different segments, all but one of those pending copies should be removed (they are true redundant duplicates of each other).
- At least one copy of every record must always survive deduplication.

## Why This Matters

During background indexing, it is normal for records to temporarily exist in both a pending state (in a new segment) and an indexed state (in an older segment). The deduplication cleanup must be aware of this distinction to avoid making records disappear from query results during the indexing window.

A test helper function that creates segments with a configurable pending threshold is also needed so this behavior can be reliably exercised in tests.
