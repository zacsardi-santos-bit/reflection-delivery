## Description

When reading JSON or CSV files using SQL queries, there is currently no way to specify the schema (column data types) inline in the SQL statement. The Python API already supports passing a schema when reading these file formats, but this capability is missing from the SQL table functions for reading JSON and CSV data.

## Expected Behavior

- The SQL function for reading JSON files should accept an optional schema argument that lets users define the data type for each column in the file being read.
- The SQL function for reading CSV files should accept the same optional schema argument.
- The schema should be expressible as a mapping of column names to type descriptors directly within the SQL query.
- A query using a schema argument should produce the same result as calling the equivalent Python read function with an explicit schema.

## Why This Matters

Without schema support in SQL reads, users are forced to either rely on automatic type inference (which may not match their needs) or add extra post-read casting steps. Allowing schema specification directly in the SQL query makes the SQL interface consistent with the Python API and gives users full control over column types at read time.
