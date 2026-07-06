## Description

Field extraction on dictionary-encoded struct columns does not work correctly. When a struct column is dictionary-encoded — a common storage optimization where repeated struct values are stored once and referenced by an integer index — attempts to access individual fields from those structs fail or produce incorrect results.

This is a real-world scenario: data ingested from Parquet files or Arrow IPC streams frequently arrives with dictionary encoding applied to struct columns for efficiency. DataFusion should be able to handle this transparently.

## Expected Behavior

- Extracting a named field from a dictionary-encoded struct column should return correct values.
- The result of field extraction should itself be dictionary-encoded, preserving the efficiency of the original representation (e.g., extracting a text field from a dictionary of structs should yield a dictionary of text values).
- Rows where the dictionary key is null should produce null for any extracted field.
- The extracted fields should support standard SQL operations: filtering, aggregation, and ordering.
- Attempting to extract a field that does not exist in the struct definition should produce an error.

## Why This Matters

Users working with data that has been dictionary-encoded at the struct level cannot currently use field access syntax to retrieve struct members. This blocks common query patterns like filtering on a struct field, grouping by a struct field, or projecting individual struct fields from dictionary-encoded columns. The fix should make dictionary-encoded structs behave consistently with their non-encoded equivalents.
