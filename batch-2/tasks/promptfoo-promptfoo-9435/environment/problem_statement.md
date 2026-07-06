# Variable Column Order Is Non-Deterministic in Evaluation Results

## Description

When running evaluations with multiple test cases — especially when tests execute concurrently — the order of variable columns in the results table is not stable. Rows that finish out of order can cause the column sequence to differ between runs, and reloading saved results may present columns in a different order than what was originally shown. This makes it difficult to compare evaluation runs reliably.

## Expected Behavior

- The order of variable columns should reflect the order in which variables were declared in the test configuration, not the order in which test rows happened to complete.
- This configured order should be stored alongside the evaluation so it can be restored faithfully when results are reloaded or exported.
- When results are converted to a table for display, the persisted order should define the column sequence. Variables added dynamically at runtime or via metadata transforms should be appended alphabetically after the configured columns.
- Legacy results files that do not contain a stored column order should continue to display variables in alphabetical order (preserving existing behavior for older data).
- If the stored column order contains duplicate entries, those should be collapsed to avoid duplicate column headers.
- Falsy variable values (such as an empty string, zero, or false) must be preserved faithfully in the table, not silently replaced by an alternative display value.
- When two sources provide conflicting values for the same variable column, the discrepancy should be surfaced via a debug log.

## Why This Matters

Consistent column ordering is essential for side-by-side comparison of evaluation runs and for users to navigate large result tables without disorientation. Without this, any re-run or reload can scramble the column layout, breaking mental models users build around specific column positions.
