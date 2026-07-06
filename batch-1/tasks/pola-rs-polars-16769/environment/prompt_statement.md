I'm working on finalizing a breaking change in Polars around how left joins handle key columns by default. The old behavior was to automatically merge (coalesce) the join key columns from both sides into one column in the output, and a deprecation warning was emitted when this happened implicitly. That deprecation period is now over, and I need to make the new non-coalescing behavior the permanent default.

With the new default, a left join should include both the left and right join key columns in the output separately, just like other join types. The right-side key column should appear with a suffix to avoid name collisions. Users who want the old single-key-column behavior need to explicitly opt in by passing a coalesce option.

The deprecation warning for doing a left join without specifying a coalesce preference should be removed entirely — no warning should be emitted since the new behavior is now the standard. Existing code that relied on the old coalescing default will need to add an explicit coalesce flag to preserve its behavior.

This change should work consistently across both streaming and non-streaming execution modes, and should apply to both the eager and lazy APIs. The output shapes and column counts for left joins without explicit coalescing will increase because the right-side key column is now included in the result.
