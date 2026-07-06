## Description

IoTDB supports loading time-series data files directly into the database, and when the column types in the file don't match those of the target table, the system is supposed to automatically convert them. However, this automatic type conversion during file loading only works for the older hierarchical (tree) data model — it does not work for the newer relational (table) data model.

Users who manage their data using the table model and have data files where column types differ slightly from the table definition cannot load those files successfully today. The load fails instead of performing the expected type conversion, making it impossible to ingest files with minor type mismatches.

## Expected Behavior

- When a data file is loaded into a table-model table and the file's column data types differ from those of the target table schema, the system should automatically convert the data types and load all rows successfully.
- After the load, querying the table should return the complete set of rows from the file — none should be silently dropped due to type mismatch.

## Related Issue

There is also a related problem in the pipe-with-load feature: when data is piped between nodes and involves a load operation, data from some source tables is being silently dropped rather than transferred completely to the receiver. After both fixes, querying the receiver should show all rows from all source tables, with null values in columns that are not applicable to a given row.

## Why This Matters

Without type conversion support in the table model, users must pre-process their data files to exactly match table schema types before loading — an unnecessary burden that already works automatically in the tree model. Fixing both issues ensures consistent, complete data ingestion across all use cases.
