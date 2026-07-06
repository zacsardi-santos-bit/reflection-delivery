## Description

When a min/max skip index is defined on a JSON-typed column, comparison queries using greater-than, equal-to, or less-than operators do not properly utilize the skip index for granule pruning. Instead of skipping data granules whose min/max range cannot satisfy the filter condition, the engine scans all granules, defeating the purpose of the index.

## Root Cause

The JSON column type implementation is missing a correct method for computing minimum and maximum values over a specified row range. The existing stub ignores the range boundaries (start/end row parameters) and always returns the first row's value for both min and max — or an empty value when the column is empty. As a result, the min/max recorded for each granule is incorrect, and the skip index cannot make accurate pruning decisions.

## Expected Behavior

- A min/max skip index on a JSON column should correctly record the minimum and maximum JSON values for each granule.
- Comparison queries against a JSON column with a min/max skip index should prune granules that cannot contain matching rows, reducing the number of granules read.
- For a table with one row per granule and three distinct JSON values, a greater-than, equal-to, or less-than filter against the middle value should read only 1 of 3 granules.
- Query results must be correct: only rows satisfying the filter condition are returned.

## Why This Matters

Without proper min/max computation for JSON columns, skip indexes on those columns provide no performance benefit, and query results may also be incorrect if the index is used but returns wrong data. Fixing the range-aware min/max computation allows the skip index to work as intended for JSON columns, improving both correctness and query performance.
