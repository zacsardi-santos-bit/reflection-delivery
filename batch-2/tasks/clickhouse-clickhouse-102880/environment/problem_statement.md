## Description

ClickHouse supports text indexes with different tokenizer modes. One of these modes stores each column value as a single whole token in the index dictionary, rather than splitting values into word-level sub-tokens. There is also an existing optimization that, when explicitly enabled via a query setting, scans the index dictionary to find which tokens match a wildcard substring pattern and uses that information to skip data blocks that cannot contain matching rows.

However, this optimization currently ignores the whole-value tokenizer mode. When a text index is created with that tokenizer mode, enabling the optimization has no effect: queries still perform a full data scan instead of using the index to skip irrelevant parts and granules.

## Expected Behavior

- When the LIKE/ILIKE dictionary scan optimization is enabled, it should also apply to text indexes that use the whole-value tokenizer mode.
- For LIKE queries (case-sensitive), the index should skip all data blocks that cannot contain the pattern.
- For ILIKE queries (case-insensitive), the same skipping behavior should apply, including for patterns given in a different case than the stored values.
- Enabling the optimization must not change query results — it must only affect which data blocks are read.
- A pattern that cannot match any token in the dictionary should cause the query to skip all data blocks entirely (zero parts and granules scanned).
- A pattern matching only some tokens should scan only the relevant data blocks.

## Why This Matters

Users who rely on the whole-value tokenizer for substring matching currently cannot benefit from the index optimization even though the dictionary contains all the information needed to prune irrelevant data. Extending the optimization to cover this tokenizer makes wildcard queries on such columns significantly more efficient without requiring any schema changes.
