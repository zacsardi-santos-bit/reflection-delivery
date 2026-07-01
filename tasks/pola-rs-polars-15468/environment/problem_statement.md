## Description

The "top k" and "bottom k" operations on expressions are limited to working on a single column, and there's no way to control sort direction. This makes it impossible to retrieve multiple columns together based on a ranking derived from one particular column — a common data analysis pattern.

## Expected Behavior

- It should be possible to specify which column (or columns) to use as the ranking key when selecting the top or bottom k rows. When a ranking key is specified, all selected columns should be returned for those k rows together.
- The sort direction should be configurable — ascending or descending. When multiple ranking keys are given, the direction should be specifiable per-column as a list.
- Both operations should work correctly within grouped aggregations.
- If a list of sort directions is provided but no ranking key is specified, a clear error should be raised explaining that a single boolean direction is required when no ranking key is given.
- If the number of sort directions does not match the number of ranking key columns, a clear error should be raised indicating the length mismatch.

## Why This Matters

Without this capability, users must write verbose workarounds (joining, sorting entire dataframes, slicing) just to get the top or bottom k rows of a grouped dataset ranked by one column while displaying another. Adding ranking-key and sort-direction support makes these operations much more flexible and consistent with how similar operations work elsewhere in the API.
