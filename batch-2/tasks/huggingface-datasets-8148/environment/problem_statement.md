## Description

The datasets library currently has no built-in support for loading data from Apache Iceberg tables. Users who store training data in Iceberg catalogs must write custom loading code instead of using the standard dataset loading interface. We should add an "iceberg" packaged module so that users can load Iceberg data with the same API they use for other supported formats.

## Expected Behavior

- Passing a catalog object and a table identifier to the standard dataset loader should return a dataset with all columns and rows from that table.
- Column projection should be supported: passing a list of column names should return only those columns.
- Row filtering should be supported: passing a SQL-style filter string should return only matching rows.
- Multiple splits should be supported: passing a mapping of split names to table identifiers should return a dataset with one split per entry.
- Streaming mode should be supported: the result should be an iterable dataset when streaming is requested, and a regular in-memory dataset otherwise.
- Time-travel queries should be supported: passing a snapshot identifier should load the table as it existed at that historical point.
- Parallel loading should work correctly when multiple worker processes are requested.
- If the catalog argument is missing or not provided, a descriptive error must be raised that mentions "catalog".
- If the table argument is missing or not provided, a descriptive error must be raised that mentions "table".

## Why This Matters

Apache Iceberg is widely used for large-scale analytical data storage, and many ML practitioners keep their training datasets in Iceberg tables. Without native support, users cannot leverage the ecosystem features (streaming, column projection, filtering, snapshot time-travel) that make the library useful for large datasets.
