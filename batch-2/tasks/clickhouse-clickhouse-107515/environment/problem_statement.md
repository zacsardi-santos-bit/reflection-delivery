## Description

Queries against partitioned tables crash with a "Not-ready Set" error when a subquery-based membership check is wrapped inside a larger expression in the WHERE clause.

For example, querying a table that is partitioned by a column and filtering with a pattern like "is the value a member of a subquery result, compared to zero" — where the membership check is not the top-level WHERE predicate but is nested inside an arithmetic comparison — causes ClickHouse to fail during partition pruning with an error indicating that an unbuilt membership set was encountered as an argument to the membership-check function.

## Expected Behavior

- Queries with a subquery-based inclusion check wrapped inside a comparison (rather than used directly as the WHERE predicate) should execute without errors on partitioned tables and return correct results.
- Queries with a subquery-based exclusion check in the same wrapped pattern should also work correctly.
- Distributed variants of these membership checks (which are intentionally deferred until after partition pruning) must also be handled gracefully — they should not crash partition pruning either.
- Top-level (non-wrapped) membership checks against partitioned tables should continue to work as before (no regression).

## Why This Matters

This is a regression for users who write filter conditions where a subquery-based membership test is used as an operand in a larger expression rather than directly as a standalone predicate. The root cause is in partition pruning: the code tries to evaluate the membership set during key analysis, but that set has not been built yet at that point. The fix should make partition pruning silently skip the condition when it encounters an unbuilt set rather than crashing.

The same issue also affects distributed membership checks, whose sets are intentionally filled in later during query execution — these must be excluded from the partition key analysis chain entirely.
