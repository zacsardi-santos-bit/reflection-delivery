## Description

When using the sorting-prefix fill feature together with a collated sort column, strings that are considered equal under the collation (e.g., 'a' and 'A' under a case-insensitive locale) are incorrectly treated as belonging to different fill groups. As a result, each distinct spelling gets filled independently over the entire numeric range, producing far more output rows than expected.

## Example

A table with four rows — two using different letter-case spellings of the same letter (collation-equal under a case-insensitive locale, e.g. lowercase 'a' paired with numeric value 1, and uppercase 'A' paired with numeric value 2) plus two rows using a distinct letter with numeric values 1 and 3 — when ordered by the string column using a case-insensitive collation and with numeric fill from 1 to 4, should produce 6 rows: one group for the collation-equal spellings and one group for the distinct letter. Instead, the current behavior produces 9 rows because the two spellings are treated as separate groups and each gets the full fill range applied independently.

## Expected Behavior

- Strings that compare as equal under the specified collation must form a single fill group.
- This must work correctly for both small and large datasets (the boundary between the two internally affects the algorithm used to find group boundaries).
- Fill group identity must survive processing across multiple data blocks, so no spurious fill rows appear when collation-equal values span chunk boundaries.
- The fix must apply to both ascending and descending collated orderings.

## Why This Matters

Users who sort by a locale-collated string column and use range filling to generate complete ordered sequences end up with incorrect, inflated results. The output row count is not predictable and intermediate fill values are duplicated for each distinct byte representation of what is logically a single value under the collation.
