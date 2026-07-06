## Description

There is a bug in ClickHouse where using the wildcard column selection with exclusions causes a crash when the excluded column name is also used as an alias for a computed expression in the same SELECT statement.

For example, a query like "select everything except column c, and then define c as a computed expression" should work perfectly — the excluded original column is dropped, and the alias provides the new computed value for c. Instead, the query engine throws an internal logical error about a type mismatch.

The root cause is that during the query normalization phase, alias substitution happens inside the exclusion column list before that list has been fully resolved. The internal alias substitution logic incorrectly replaces the column name in the exclusion clause with the aliased computed expression before the exclusion list is expanded, causing a type mismatch when the exclusion list is subsequently processed.

## Expected Behavior

- Queries using wildcard selection with column exclusions, where the excluded column name matches an alias in the same SELECT, should execute successfully and return the correct results.
- This should work in all common query patterns:
  - Direct queries on a table
  - Subqueries used in JOINs
  - CTEs (Common Table Expressions) referenced via JOINs
  - Multiple columns listed in the exclusion clause
- Both the legacy query analyzer and the new experimental analyzer should handle these queries correctly and produce identical results.

## Why This Matters

This query pattern is a natural and ergonomic way to "replace" a column with a computed version while keeping all other columns. It is reported as a real production incident. The crash is unexpected and forces users to rewrite queries in a more verbose form.
