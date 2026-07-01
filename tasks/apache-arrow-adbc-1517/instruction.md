Implement the database object discovery functionality in the Flight SQL driver for Apache Arrow ADBC. Ensure that the driver correctly retrieves and structures metadata about catalogs, schemas, tables, and columns from a Flight SQL server. Additionally, update the connection info retrieval method to make actual server requests when specific info codes are requested.

Requirements:

* Implement the `getObjects()` method in `FlightSqlConnection.java`:
    * Signature: `getObjects(AdbcConnection.GetObjectsDepth depth, String catalogPattern, String dbSchemaPattern, String tableNamePattern, String[] tableTypes, String columnNamePattern) throws AdbcException -> ArrowReader`
    * For depth `CATALOGS`:
        * Return an `ArrowReader` where vector 0 contains catalog names and vector 1 is null.
        * Match the row count to all catalogs returned by the server.
        * Apply `catalogPattern` to filter results; return no batches if no match.
    * For depth `DB_SCHEMAS`:
        * Vector 1 must be a `ListVector` of schema structs with 'db_schema_name'.
        * Ensure 'db_schema_tables' is absent at this depth.
        * Include catalogs with empty schema lists if no schemas exist.
    * For depth `TABLES`:
        * Include 'db_schema_tables' with 'table_name' and 'table_type' fields.
        * Ensure 'table_columns' is absent at this depth.
        * Include schemas with empty 'db_schema_tables' if no tables exist.
    * For depth `ALL`:
        * Include 'table_columns' with 'column_name', 'ordinal_position', 'xdbc_is_nullable', and type-specific fields.
        * Apply `columnNamePattern` to filter columns; ensure non-null empty list if no match.
        * Ensure 'table_columns' is null at `TABLES` depth regardless of column filter.

* Update the `getInfo()` method in `FlightSqlConnection.java`:
    * Signature: `getInfo(int[] infoCodes) throws AdbcException -> ArrowReader`
    * Ensure server-side requests are made for specific info codes to transmit authorization headers.

* Implement `GetObjectsMetadataReaders` class:
    * Location: `GetObjectsMetadataReaders.java`
    * Provide `CreateGetObjectsReader()` method to return appropriate `ArrowReader` based on `GetObjectsDepth`.
    * Handle SQL-to-regex pattern conversion and helper methods for xdbc fields.

* Implement `GetInfoMetadataReader` class:
    * Location: `GetInfoMetadataReader.java`
    * Provide `CreateGetInfoMetadataReader()` method to retrieve connection info from the server.
    * Translate results into ADBC GET_INFO_SCHEMA format.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.