## Description

The query optimizer for primary key lookups is too limited when dealing with OR-based conditions on the primary key column. When a query combines multiple primary key equality checks using OR — for example, "fetch all rows where the primary key equals any of these several values" — the optimizer cannot extract those candidate values to enable efficient data block pruning. Instead, it falls back to scanning far more data than necessary, defeating the purpose of zone map and bloom filter indexes.

## Expected Behavior

- When a query's WHERE clause contains OR expressions that all reference the same primary key column, the optimizer should be able to extract the full set of candidate primary key values.
- OR combinations of simple equality checks, IN-list expressions, and nested OR/AND expressions involving the primary key should all be handled correctly.
- The extracted candidate values should be usable for zone map and bloom filter checks, so irrelevant data blocks can be skipped.
- The block-level iteration API should provide both block-level and object-level metadata to its callback, so callers have all the context they need without separate lookups.

## Why This Matters

Queries that reference a small, known set of primary key values via OR conditions are common, and they should be fast. Without this capability, the database must scan significantly more data than needed, resulting in poor query performance for what should be highly efficient lookups. Users expect that filtering by a set of specific primary key values — whether via IN lists or OR chains — results in targeted, efficient reads.
