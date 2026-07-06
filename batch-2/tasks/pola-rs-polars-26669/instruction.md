I'm trying to write Polars DataFrames to Parquet files using a custom Arrow schema that matches my Iceberg table schema.

*   When sink_parquet is called with an arrow_schema that contains a fixed-size binary field, a Polars Binary column must be successfully converted and written as that fixed-size binary type in the output Parquet file.

*   When a binary column value has a byte length that does not match the target fixed size, sink_parquet must raise a ComputeError with a message that includes 'bytes at index {i} had mismatching length {len}' (where i is the 0-based index of the first mismatching value and len is its actual byte length). For example, attempting to write [b"A", b"BB"] to a fixed-size binary field of size 1 must raise ComputeError with a message matching 'bytes at index 1 had mismatching length 2'.

*   Null values in a binary column must be preserved as nulls (not treated as errors) when converting to a fixed-size binary type during sink_parquet.

*   After a successful sink_parquet with a fixed-size binary arrow_schema, reading back the Parquet file with scan_parquet must produce a DataFrame matching the original input data, including nulls.

*   The schema of the Parquet file produced by sink_parquet must match the arrow_schema provided, so that the written file has the fixed-size binary field type rather than the variable-length binary type.

*   When sink_parquet is called with an arrow_schema containing a Time64(Microsecond) field (as produced by Iceberg schema conversion with field ID metadata), a Polars Time column must be successfully converted and written without error.

*   sink_parquet with an arrow_schema derived from an Iceberg schema must support a complete round-trip for all Iceberg primitive types, including Boolean, Integer, Long, Float, Double, Date, Time, Timestamp, TimestampTZ, String, Binary, Decimal, Fixed (fixed-size binary), and UUID. Data written to Parquet via sink_parquet with the Iceberg-derived arrow_schema and then read back via scan_iceberg must produce a DataFrame equal to the original input.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.