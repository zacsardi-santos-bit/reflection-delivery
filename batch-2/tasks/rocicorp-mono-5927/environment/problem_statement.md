## Description

The data query layer currently has no efficient way to look up rows by a specific set of key-value combinations in a single fetch operation. When a join needs to retrieve a batch of parent rows identified by multiple child-side keys, the only option is to issue individual single-key lookups or to fetch more data than necessary and filter it downstream. This is both inefficient and difficult to optimize at the storage layer.

## Expected Behavior

- A fetch request should support an optional list of multi-value IN constraints, where each entry in the list specifies a set of column-value pairs a row must match.
- When multiple IN constraint lists are provided, they should all be applied together (ANDed): only rows matching at least one entry in every list are returned.
- An empty list of IN constraints should be a no-op (all rows are returned).
- An empty entry within the list should be silently ignored, not treated as a non-matching constraint.
- These IN constraints should compose correctly with existing equality filters, pagination cursors, and sort direction (including reverse).
- Null values in the IN list should never match stored null values, following standard SQL null semantics.
- Compound multi-column IN combinations should be supported: a row must match all specified columns in at least one entry.
- During live writes (when a pending change is being applied), the same IN constraints should be applied to the overlay — the pending row is included or excluded based on whether it matches the IN list, and the add and remove sides of an edit are each evaluated independently.
- The SQL query builder should generate correct and index-friendly SQL for both single-column and compound-column IN lists.

## Why This Matters

Without this capability, join operators that need to batch-fetch rows by a list of keys must fall back to row-by-row fetching or full scans, which is significantly less efficient. Batched key lookup enables the query planner to issue a single efficient indexed query to retrieve all relevant parent rows for a given set of child-side foreign-key values.
