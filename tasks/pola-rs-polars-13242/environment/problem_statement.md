## Description

The SQL interface for Polars dataframes currently only supports joining two tables on a single column pair. Attempting to join on multiple columns simultaneously fails with an error indicating that only basic single-condition equi-joins are supported. This is a significant limitation because composite-key joins are extremely common in real-world data analysis.

## Expected Behavior

- SQL join syntax that combines multiple equality conditions using AND should be supported for both inner and left-style joins.
- The correct rows should be returned — only those where all specified column pairs match (for inner joins), or all left-side rows with NULLs where right-side conditions are unmet (for left joins).
- The join conditions should work even when the matching columns have different names on each side.
- More than two equality conditions chained with AND should be handled correctly.
- Multiple sequential joins, each with compound conditions, should all work together.
- Compound join conditions within subqueries should also be supported.
- Malformed conditions — such as column references that are not fully table-qualified, or expressions that are not equality comparisons — should continue to be rejected with an error.

## Why This Matters

Real datasets frequently have composite primary keys (for example, a combination of an ID column and a date column). Without multi-column join support, users cannot express natural SQL that reflects these data relationships, forcing awkward workarounds or abandoning the SQL interface entirely.
