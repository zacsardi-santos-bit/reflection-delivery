## Description

When computing skewness and kurtosis reductions on data that uses a boolean validity mask to distinguish between genuinely absent values and numeric not-a-number values, the computation incorrectly treats the two as equivalent. Specifically, if a data array uses a separate mask to indicate which entries are absent, and a floating-point not-a-number appears at a position where the mask says the entry is valid, that not-a-number should be treated as valid data (and cause the result to be not-a-number through arithmetic propagation). Instead, the current implementation skips it as though it were an absent value, producing an incorrect non-missing result.

This affects both the low-level numerical operations (skewness and kurtosis) and the higher-level grouped reductions built on top of them. Other reductions (sum, mean, variance, etc.) already handle this correctly — only skewness and kurtosis have this inconsistency.

## Expected Behavior

- When a mask is provided alongside an array of values, the mask is the authoritative indicator of which entries are absent. Entries marked absent in the mask are missing; all other entries — including those whose value is not-a-number — are valid data.
- With "skip missing values" enabled, only mask-marked absent entries are skipped; numeric not-a-number values that are not masked as absent propagate not-a-number through the computation.
- With "skip missing values" disabled, any absent entry (mask=True) encountered causes the result to immediately become not-a-number.
- Grouped skewness and kurtosis reductions on nullable floating-point data must be consistent with other reductions like sum and mean.
- When a future option to distinguish not-a-number from genuine absent values is active and "skip missing values" is disabled, grouped reductions must propagate genuine absent values as absent in the result.

## Why This Matters

Users relying on nullable floating-point arrays for precise NA semantics expect that skewness and kurtosis behave the same as sum, mean, and variance when deciding whether to skip or propagate missing values. The inconsistency means that groupby skewness and kurtosis silently return wrong answers for data containing not-a-number values in a nullable array.
