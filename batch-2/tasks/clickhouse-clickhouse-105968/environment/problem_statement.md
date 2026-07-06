## Description

When using the "single value or null" aggregate function with the semi-structured document column type in ClickHouse, the server crashes with a segmentation fault during deserialization of aggregate states stored in a persistent aggregate-storage table. This makes it impossible to safely persist and later read these aggregate states for document-typed columns.

## Steps to Reproduce

1. Enable the semi-structured document column type feature
2. Create a persistent aggregate-storage table with an aggregate-function column that stores "single value or null" states over document values
3. Insert rows into the table using the state combinator
4. Query the table using the merge combinator to retrieve results

## Expected Behavior

- The "single value or null" aggregate function should work correctly in memory with the semi-structured document column type: returning the column value when all inputs are identical, and NULL when inputs differ
- Reading and merging persisted aggregate states from a persistent aggregate-storage table should complete successfully without crashing the server
- After deserialization, the merged result returns NULL (a known pre-existing limitation where the serialization path does not persist internal state flags)

## Actual Behavior

The server crashes with a memory access violation during the deserialization step when reading aggregate states for the semi-structured document column type from persistent aggregate storage.

## Why This Matters

Users cannot safely use the "single value or null" aggregate function with document-typed columns in materialized aggregations or persistent aggregate-storage scenarios. The server crashes rather than returning a result.
