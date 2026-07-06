## Description

The Flight SQL driver for Apache Arrow ADBC is missing a working implementation for database object discovery. When users try to retrieve metadata about catalogs, schemas, tables, and columns from a Flight SQL server, the driver either returns incorrect results or fails to build the expected hierarchical structure. The connection info retrieval also has a bug where it does not actually reach the server when specific info codes are requested, meaning authentication headers are never verified against real server calls.

## Expected Behavior

- Retrieving catalog metadata should return a properly structured list of all catalogs from the server, supporting an optional filter that restricts results to catalogs matching a given pattern. A filter that matches nothing should yield an empty result set.
- Retrieving schema metadata should nest schemas under their parent catalog. Catalogs that have no schemas must still appear in the output with an empty schema list. Multiple schemas within one catalog must each appear as a separate entry.
- Retrieving table metadata should nest tables under their parent schema, which is in turn nested under its catalog. Schemas with no tables must appear with an empty table list. Column metadata must be absent at the table listing depth.
- When retrieving full object metadata including columns, each column entry must include the column name, its 1-based position, nullability (as "YES" or "NO"), and type-specific precision/scale information. When a column filter is applied and no columns match, the column list for each table should be present but empty, not absent. 
- The connection info retrieval method, when called with specific info codes, must make real requests to the server so that authentication and other request headers are actually transmitted.

## Why This Matters

Without this feature, users of the Flight SQL ADBC driver cannot programmatically inspect the database schema to discover available tables, schemas, and columns — a fundamental capability for database tooling, BI tools, and SQL clients that rely on catalog introspection.
