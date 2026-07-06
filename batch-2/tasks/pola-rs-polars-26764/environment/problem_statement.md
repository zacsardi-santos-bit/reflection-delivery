## Description

When writing Parquet files that contain string or binary columns with very large values, the resulting file is enormously bloated even for tiny data tables. This happens because the column statistics metadata — which stores the minimum and maximum values found in each column chunk — includes the full, untruncated large values. A single-row table containing a one-million-character string can produce a Parquet file that is megabytes in size just because of the statistics, even though the actual data compresses well.

## Expected Behavior

- Writing a data table with a large string or binary value to Parquet should produce a compact file. The statistics metadata should be truncated to a reasonable maximum length (64 bytes by default), so the file size stays small.
- For text (Unicode) columns, the truncation must respect character boundaries, ensuring the stored statistics values remain valid text. The truncated minimum should be the longest valid prefix that fits within the limit; the truncated maximum should be that prefix with its last character incremented to form a valid upper bound.
- For raw binary columns, truncation should happen at the byte level. The maximum bound should be produced by incrementing the last byte that is not already at its maximum value.
- Short values that already fit within the limit should be stored without modification.
- An environment variable should allow users to customize the truncation length or disable truncation entirely (by setting the variable to zero).

## Why This Matters

Users storing data with large string or binary values are surprised to find their Parquet files are much larger than expected. The bloat comes entirely from untruncated statistics metadata, not the actual data. Truncating statistics is standard practice in Parquet implementations and results in dramatically smaller files with no loss of correctness, since the statistics are still valid (if less precise) bounds on the column values.
