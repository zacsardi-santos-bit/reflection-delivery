## Description

The column data structure in the chunk package maintains an integer field to cache the count of null values. This cached count must be kept in sync with the underlying null bitmap every time a null flag is set, cleared, or rows are appended, truncated, or reconstructed. In practice, this synchronization is fragile: several code paths update the bitmap but forget to adjust the count, or adjust it incorrectly, leading to stale or incorrect null counts.

The fix is to remove the cached null-count field entirely and replace it with a method that computes the count on demand directly from the null bitmap. This guarantees the count is always accurate.

## Expected Behavior

- The null count should be a computed value derived from the bitmap, not a cached integer field.
- All existing operations (append, truncate, reconstruct, encode/decode) must continue to work correctly without maintaining a separate count.
- New bulk pre-allocation operations should be available for each fixed-length numeric and decimal type, allowing a block of rows to be initialized all at once as null. After such a pre-allocation, all rows in the block should be null and the null count should equal the number of pre-allocated rows.
- It should be possible to selectively mark individual pre-allocated rows as not-null, with the null count reflecting each change accurately.
- A subsequent append after pre-allocation should add one non-null row at the end of the block, leaving the pre-allocated null rows intact.

## Why This Matters

Keeping a redundant cached count in sync with the bitmap adds complexity and is a latent source of bugs. Removing the field simplifies the code and makes null accounting self-consistent. The new bulk pre-allocation capability is also useful for callers that want to reserve a block of rows upfront and fill them in selectively, rather than appending values one by one.
