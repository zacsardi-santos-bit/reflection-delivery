## Description

The physical query optimizer incorrectly handles sort elimination for execution plans that contain window aggregation operations. When a query includes a window function followed by a sort requirement, the optimizer sometimes fails to remove sorts that are provably redundant, and in other cases it may eliminate sorts that should be preserved. This leads to suboptimal (or, in rare cases, incorrect) execution plans.

The core issue is that the optimizer does not correctly account for the ordering properties that different window functions guarantee. Specifically:

- When a window covers the entire table with no partitioning, its output value is constant for all rows, making any downstream sort on that output unnecessary.
- When a window has partitioning by an already-ordered column, the output is constant within each partition — a form of partial constantness — which can also allow certain sorts to be eliminated.
- When a window function has set-monotonic properties (meaning its running result is guaranteed to increase or decrease as rows are added to the window frame), and the frame type and input ordering are compatible, the output column has a deterministic ordering that can satisfy downstream sort requirements.
- When the window function lacks monotonicity or operates on an unordered column, sorts cannot be inferred and must be preserved.

## Expected Behavior

- Sorts above a window aggregation should be removed when the window's output ordering (given its frame type, function monotonicity, and input column ordering) already satisfies the sort requirement.
- When the window function or input ordering cannot guarantee the required output order, the sort must be kept in the plan.
- Both unbounded-frame and bounded-frame window aggregation node types must be handled correctly.
- Plans with multiple chained window operations must be optimized correctly without cascading incorrect decisions.

## Why This Matters

Window functions appear frequently in analytical queries (running totals, rankings, moving averages). Having the optimizer correctly reason about their output ordering properties allows unnecessary sorts to be eliminated, reducing query execution time. This fix also prevents the opposite bug where sorts are incorrectly removed, which could produce wrong results.
