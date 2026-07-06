# ANY RIGHT JOIN with OR conditions returns wrong results when table swap is disabled

## Description

There is a correctness bug in the hash-join engine when executing a right-side "any match" join that has multiple OR-connected conditions in the ON clause. When the query plan optimizer's table-swap feature is active (which rewrites the query into an equivalent left-side join), the results are correct. However, when that optimization is disabled — either explicitly or because the query shape prevents it — the join engine follows the actual right-join code path and returns incorrect or incomplete results.

The root cause is that the engine's internal loop over multiple OR condition maps exits too early: it stops searching after the first OR condition produces a match, and never checks the remaining OR conditions. As a result, right-side rows that are only reachable via a second (or later) OR condition are silently omitted from the output.

## Expected Behavior

- A right-side "any match" join with OR conditions should produce the **same** complete result set regardless of whether the query plan optimizer rewrites it as a left-side join.
- When multiple OR conditions are present, the engine must continue scanning all OR condition maps for each right-table row before committing the result.
- A right-table row should appear in the output paired with all left-table rows that match via **any** of the OR conditions.

## Why This Matters

Users who explicitly disable the table-swap setting, or whose queries are structured in a way that prevents the optimizer from applying the rewrite, will silently get wrong answers for a common and valid join pattern. The bug is not obvious because the default optimizer path produces correct results, masking the underlying defect.
