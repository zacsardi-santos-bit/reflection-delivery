## Description

Rolling window operations with grouping produce silently incorrect results when the input DataFrame is sorted by the grouping column rather than by the time index column.

When data is pre-sorted by the group identifier, the rolling window computation runs without errors, but the group keys in the output are misaligned with the aggregated values — rows end up attributed to the wrong groups. This is especially dangerous because no error or warning is raised; the results simply appear plausible while being wrong.

## Expected Behavior

- Rolling window aggregations with a grouping parameter should always produce output where each row's group key correctly matches the computed aggregation for that group and time window.
- The ordering of the input (whether sorted by time or by group) should not affect the correctness of group key assignments in the output.
- Empty time windows within a group should still be attributed to the correct group, not an incorrect offset position.

## Reproduction

Given a small DataFrame with two groups (ids 1 and 2) and distinct timestamps, sorting by the group id column and then applying a rolling window grouped by that same id column returns group ids that do not match the expected order.

## Why This Matters

Users who organize their data by group before performing rolling window analyses — a perfectly reasonable workflow — silently receive wrong results. This undermines the reliability of any time-series analysis that uses grouping with rolling windows.
