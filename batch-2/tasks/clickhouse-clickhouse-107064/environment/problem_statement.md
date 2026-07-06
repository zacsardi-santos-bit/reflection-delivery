## Description

ClickHouse has a bug when performing a compound column rename that reuses a freed column name between columns of different types — commonly called a "column name swap." When two columns are renamed simultaneously such that each takes the other's name (or one column is dropped and another is renamed into its freed name), and the two columns have different types, the resulting storage part records the wrong type in its column metadata.

The data files are renamed correctly, but the column type declarations are inherited from whichever column previously held that name rather than from the actual source of the rename. This produces a mismatch between the stored data and the declared type, causing read-time conversion errors or crashes when loading or querying the affected part.

## Expected Behavior

- A compound rename that swaps column names between columns of different types should record each column's own actual type in the part metadata, not the type of the column that previously held that name.
- This must work correctly for both compact and wide storage formats.
- If one of the swapped columns was never materialized (added to the schema but never filled), the operation must complete without error; the unmaterialized column should be absent from the part and default-filled at read time.
- Combining such a rename swap with other simultaneous mutations (like an UPDATE) in the same ALTER statement must also work correctly.
- Dropping a column and renaming another column into the freed name in a single ALTER — when the two columns have different types — must similarly assign the correct type to the renamed column.

## Why This Matters

Users who perform multi-step schema migrations using compound RENAME COLUMN statements — a common pattern when changing the type of a column in-place or swapping roles between columns — can end up with corrupted table parts that crash or produce incorrect results on read. This is a correctness regression that silently stores the wrong type metadata, leading to hard-to-diagnose read failures.
