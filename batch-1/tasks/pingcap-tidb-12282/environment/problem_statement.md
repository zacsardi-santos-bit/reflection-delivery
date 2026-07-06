## Description

The SQL query optimizer's fast single-row lookup path is silently skipped whenever a query uses a table alias. This means that queries which qualify for direct, highly efficient single-row access — but happen to assign an alias to the table — fall back to slower execution plans. It also means that some valid alias-based queries may produce incorrect results or behave inconsistently compared to their non-aliased equivalents.

## Expected Behavior

- Single-row lookup optimization should apply to queries that use a table alias, just as it does for queries without aliases.
- Column references qualified by the alias (in both the SELECT list and the WHERE clause) should be correctly resolved when the optimizer uses the fast path.
- Selecting both a wildcard and named columns in the same query should work correctly and return all expected columns in the right order.
- When a table alias is given, attempting to reference the original table name (instead of the alias) in a WHERE clause or SELECT list should produce an appropriate error — consistent with standard SQL behavior where an alias completely replaces the original name.
- Queries using an entirely unknown table qualifier should also produce an appropriate error.

## Why This Matters

Developers writing queries with table aliases expect consistent behavior and optimization regardless of whether they use an alias. Silently degrading performance or producing wrong results when an alias is present makes the optimizer unpredictable and can hide bugs in applications.
