## Description

When using PRQL to query data targeting Snowflake as the SQL backend, certain grouping operations produce invalid SQL. Specifically, when grouping records and selecting a subset per group (such as the first record per group), the PRQL compiler generates a window function expression without an ORDER BY clause. Snowflake's database engine requires ranking window functions to always include an ORDER BY clause in their window specification — otherwise, the SQL is rejected as syntactically invalid.

## Expected Behavior

- When compiling a PRQL group+take query **without** any explicit sort order targeting Snowflake, the generated SQL window function should automatically include a neutral fallback ordering (using the constant value 1) so that the SQL is valid for Snowflake.
- When the user **does** provide an explicit sort inside the group+take block, that user-specified sort column should be used in the window function's ORDER BY, and no fallback should be injected.

## Why This Matters

Users who write idiomatic PRQL queries and target Snowflake currently get SQL that Snowflake rejects at runtime. The compiler should produce valid SQL for each target dialect, handling Snowflake's stricter window function requirements transparently — without requiring users to add workarounds or dummy sort expressions in their PRQL queries.
