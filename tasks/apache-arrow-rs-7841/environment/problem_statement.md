## Description

Arrow-rs currently supports 128-bit and 256-bit decimal array types for storing precise numeric values, but lacks more compact variants for data with lower precision requirements. For many real-world datasets, the full 128-bit decimal range is unnecessary, leading to wasted memory and storage.

This issue requests adding support for two new compact decimal array types:
- A **32-bit decimal** type suitable for values with up to 9 significant digits of precision
- A **64-bit decimal** type suitable for values with up to 18 significant digits of precision

## Expected Behavior

- Users can create Arrow arrays for each new decimal type from integer values, and set precision and scale on them.
- Each new decimal type can be used as a column data type when building record batches and schemas.
- When Parquet files contain columns with these new decimal types, the Parquet reader correctly reads row-group-level statistics (minimum value, maximum value, null count, row count per row group) and returns them as arrays of the appropriate decimal type.
- The statistics are reported as exact (not truncated).
- For smaller precision values (up to 9 digits), the Parquet physical storage uses 32-bit integers; for the 64-bit decimal with moderate precision (up to 18 digits), 64-bit integer storage is used. Statistics reading handles both storage encodings.
- Data-page-level statistics also work correctly for these new types, including pages that contain only null values.

## Why This Matters

These compact decimal types are important for memory-efficient analytics. Supporting their statistics in Parquet enables predicate pushdown and query planning optimizations that currently only work for the larger decimal types.
