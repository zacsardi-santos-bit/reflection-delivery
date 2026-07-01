Update the default behavior of left joins in Polars to include both the left and right join key columns in the output, unless explicitly instructed to coalesce. Remove the deprecation warning for unspecified coalescing preferences, as the new behavior is now standard.

*   Implement the default behavior for left joins:
    *   Include both left and right join key columns in the output when `coalesce` is not specified.
    *   Append a '_right' suffix to the right key column when it shares a name with the left key column.
    *   Ensure this behavior applies to both eager and lazy APIs in Python (`DataFrame.join()` and `LazyFrame.join()`).
    *   Ensure this behavior applies to both streaming and non-streaming execution modes.

*   Implement explicit coalescing behavior:
    *   When `coalesce=True` is specified in Python, merge the left and right join key columns into a single column.
    *   In Rust, use `JoinArgs` with `JoinCoalesce::CoalesceColumns` to merge key columns.

*   Remove the deprecation warning:
    *   Ensure no warning is emitted when performing a left join without specifying a coalesce preference.

*   Update output shapes:
    *   Reflect the inclusion of the right-side key column(s) in the output shape for left joins without explicit coalescing.
    *   Ensure the right-side key column appears with null values where rows from the left side have no match.

*   Ensure consistent behavior across different join scenarios:
    *   Include both left and right copies of the key column in unit (single-row) left joins.
    *   Include the right-side key column in Hive-partitioned left joins without explicit coalescing.
    *   Maintain consistent null-match behavior with the right key column included in the output.

*   Ensure Rust API compatibility:
    *   Make `JoinCoalesce` accessible from `polars_ops::prelude`.
    *   When constructing `JoinArgs` for a left join without `JoinCoalesce::CoalesceColumns`, ensure the right key column appears in the output.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.