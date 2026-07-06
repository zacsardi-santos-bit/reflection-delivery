## Description

DuckDB needs a built-in utility function that generates representative sample data for every supported data type. Currently there is no easy way to write tests that exercise all types simultaneously — developers have to craft individual queries for each type they want to test. This leads to gaps in coverage and makes it difficult to validate that storage, serialization, and type handling work correctly across the full spectrum of supported types.

## Expected Behavior

- A callable built-in function (no arguments required) must return a table with one column per supported DuckDB data type, including all integer widths and signedness, all timestamp precisions and timezone variants, floating-point types, multi-precision decimals, UUIDs, intervals, strings, binary data, user-defined enum types of varying sizes, arrays, nested arrays, structs, structs containing arrays, arrays of structs, and maps.
- The function must produce exactly three rows: one containing the minimum representable value for each type, one containing the maximum representable value for each type, and one where every column is NULL.
- Data produced by this function must be storable in a regular DuckDB table and must survive a full database restart — the persisted data must be byte-for-byte equivalent to the original function output.
- The existing logic for computing minimum and maximum representable values for dates, timestamps, time types, decimals, and enums must be corrected so that the returned boundary values are accurate and representable within each type.

## Why This Matters

Without a comprehensive all-types test fixture, regressions in type handling, storage encoding, or compression can go undetected. A single callable function that covers every type makes it straightforward to add storage-round-trip tests, compression validation tests, and type-correctness tests — all sharing the same data source. This is especially important for a database engine where adding a new type or changing internal encoding needs to be validated against all existing types.
