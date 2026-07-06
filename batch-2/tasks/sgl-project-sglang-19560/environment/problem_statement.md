## Description

The tensor comparison diagnostic tool currently reports only the signed mean value for each tensor, which can be misleading: a tensor containing large but balanced positive and negative values will show a mean near zero even though its elements have substantial magnitudes. In addition, quantile statistics are stored as four separate nullable fields, making it awkward to work with and missing the median (50th percentile) entirely. Finally, when two tensors are compared, there is no percentile breakdown of the absolute difference distribution, so it is impossible to tell from the report whether discrepancies are concentrated in a few extreme elements or distributed uniformly.

## Expected Behavior

- The per-tensor statistics should include an absolute mean — the mean of the element magnitudes — alongside the regular signed mean, so that the scale of a tensor is always visible regardless of sign balance.
- The percentile statistics for a tensor should be stored as a unified structure (a mapping from percentile rank to value) that includes the 1st, 5th, 50th, 95th, and 99th percentiles, rather than four separate nullable fields covering only four of those levels.
- The diff report should include a percentile summary of the absolute differences between the two tensors, using the same set of percentile ranks, so developers can see whether differences are concentrated at the tails or spread across the distribution.
- When a tensor is too large for quantile computation to be practical, the percentile mapping should be empty rather than populated with null values.

## Why This Matters

These additions make the diagnostic output significantly more informative for debugging subtle numerical discrepancies between model runs, particularly for tensors with mixed-sign values or when the distribution of differences matters more than their scalar summary statistics.
