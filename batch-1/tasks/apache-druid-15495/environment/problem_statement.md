## Description

There are two related problems with how the multi-stage query engine handles ingest operations (INSERT and REPLACE) that produce zero rows.

**Problem 1: Empty ingest queries fail or behave incorrectly**

When an INSERT or REPLACE query produces no output rows (for example, because a WHERE clause filters out all records), the operation currently fails with an error instead of completing gracefully. The expected behavior is:

- An INSERT that produces 0 rows should be a no-op — it completes successfully without creating any segments.
- A REPLACE that produces 0 rows should delete (via tombstones) any existing data in the OVERWRITE range, or simply complete with no changes if there is no existing data in that range.

There should also be a configurable option that, when enabled, causes empty ingest results to be treated as a fault — useful when producing zero rows is unexpected and should signal an error.

**Problem 2: Tombstone interval calculation is wrong for eternity segments**

When computing tombstone intervals for a REPLACE operation, if the existing segment's interval spans all of time (eternity), the tombstone calculation produces incorrect results. Instead of creating a single tombstone covering eternity, the code incorrectly attempts to partition the eternity end boundary by granularity, leading to wrong tombstone intervals or errors.

## Expected Behavior

- Empty INSERT queries complete successfully with no segments written.
- Empty REPLACE queries create tombstones for overlapping existing segments (if any), using the correct intervals.
- When an existing segment spans all of time and is being replaced by an empty result, the tombstone produced should span all of time.
- When the fail-on-empty option is enabled in the query context, any ingest that produces 0 rows should raise an appropriate fault.

## Why This Matters

Users running REPLACE queries with filtering conditions that may occasionally match no records need the operation to succeed cleanly and correctly clean up old data. The eternity-interval bug can cause REPLACE operations over full-timeline datasources to produce incorrect tombstone coverage, leaving stale segments behind.
