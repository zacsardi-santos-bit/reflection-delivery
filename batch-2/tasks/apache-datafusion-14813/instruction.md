Improve the physical query optimizer to correctly handle sort operations above window aggregation nodes. Ensure that the optimizer accurately determines when a sort can be safely removed based on the ordering guarantees provided by the window function.

*   Implement logic in the optimizer to:
    *   Remove sort requirements when a window aggregation covers the entire table with no partitioning, treating the output as globally constant.
    *   Eliminate sort requirements for unbounded window aggregations with partitioning by an already-ordered column, recognizing partial constantness within partitions.
    *   Infer output ordering for sliding window aggregations with an ever-receding start boundary when the window function is set-monotonic and applied to an ordered column without partitioning.
    *   Recognize output ordering for causal bounded window aggregations with increasing or decreasing set-monotonic functions applied to ordered columns, removing redundant sorts.
    *   Preserve sort requirements when the window function is not set-monotonic or when applied to a column not in the input ordering.
    *   Maintain sort requirements when partitioning expressions cannot be satisfied by input ordering.
    *   Block sort pushdown through a window node when parent sort requirements depend on window-produced columns not satisfying the requirement.

*   Ensure the optimizer:
    *   Handles both unbounded and bounded streaming window aggregation nodes correctly across all frame types.
    *   Uses the `assert_optimized` test macro with an optional case-number argument for identifying failed test cases.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.