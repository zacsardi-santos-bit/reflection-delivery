## Description

When using a group expression with no partition columns in a PRQL query alongside an inner sort transform, the compiler silently drops the sort order from the generated SQL. This means the resulting SQL has no ordering clause, even though the user explicitly requested one inside the group.

## Expected Behavior

A group with no partition columns represents a full-table operation — it applies its inner pipeline to the entire result set. Any sort specified inside it should be preserved and reflected as an ORDER BY clause in the generated SQL. When combined with a row-limiting operation, the absence of an ordering clause makes the result non-deterministic, which is incorrect behavior.

The generated SQL should include both the ordering and the limit — selecting all columns from the source table with the expected sort order applied.

## Why This Matters

Users rely on sort ordering inside group expressions to control the order of results. Silently dropping the ORDER BY clause is especially harmful when a limit is also applied, because it makes the selected rows unpredictable. An empty group with no partition should behave transparently, passing through sort and limit operations to the final SQL query.

This is tracked as issue #5100.
