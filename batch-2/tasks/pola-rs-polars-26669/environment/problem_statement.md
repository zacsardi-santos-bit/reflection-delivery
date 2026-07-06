## Description

Writing a Polars DataFrame to Parquet using a custom Arrow schema fails for certain type combinations that are essential when integrating with Iceberg tables.

Two specific conversion failures prevent this from working:

1. Converting a binary (variable-length) column to a fixed-size binary type fails with an "unsupported conversion" error. This conversion is required for Iceberg tables that use fixed-length byte sequences (e.g., Iceberg's fixed-length binary and UUID types).

2. Writing a time column when the target Arrow schema specifies microsecond-precision time with Iceberg field ID metadata also fails with a similar "unsupported conversion" error.

Together, these gaps mean users cannot write a Polars DataFrame to a Parquet file that is directly usable by an Iceberg table containing these common primitive types, making it impossible to complete a full write-then-read cycle.

## Expected Behavior

- Writing binary data to a fixed-size binary target should succeed when all non-null values have the correct byte length
- When a binary value has the wrong number of bytes for the target fixed size, a clear error should be raised indicating the 0-based index of the problematic value and its actual byte length
- Null values in a binary column should remain null when written to a fixed-size binary column — they should not cause an error
- The written Parquet file's schema should reflect the fixed-size binary type as specified by the supplied Arrow schema
- Writing time data to a microsecond-precision time column should work correctly
- A complete round-trip — writing a Polars DataFrame to Parquet using an Iceberg table's Arrow schema, then reading it back via an Iceberg scan — should produce identical data for all Iceberg primitive types

## Why This Matters

Users who maintain Iceberg tables and want to write data from Polars need reliable type conversion support for all Iceberg primitive types. Without these fixes, it is impossible to perform a full read/write cycle on Iceberg tables that use fixed-size binary (for fixed-length fields and UUIDs) or time columns.
