Implement a fallback mechanism in the MySqlSchema class to handle schema retrieval for tables with advanced index definitions. When the primary method fails, ensure the connector can still capture change data by using a simpler schema retrieval method.

*   Modify the MySqlSchema class located at `seatunnel-connectors-v2/connector-cdc/connector-cdc-mysql/src/main/java/org/apache/seatunnel/connectors/seatunnel/cdc/mysql/utils/MySqlSchema.java`.
    *   Update the constructor to accept a `Map<TableId, CatalogTable>` as the third parameter.
*   Implement the `getTableSchema(JdbcConnection jdbc, TableId tableId)` method to:
    *   Attempt to retrieve the table schema using a `SHOW CREATE TABLE` query.
    *   If the `SHOW CREATE TABLE` query fails or returns an empty result, automatically fall back to using a `DESC` query.
    *   Parse the `DESC` query result to extract column metadata:
        *   Use 'Field' for column names.
        *   Use 'Type' for data types, normalizing to uppercase (e.g., 'bigint' to 'BIGINT').
        *   Use 'Null' to determine nullability ('YES' or 'NO').
        *   Use 'Key' to identify primary ('PRI') and unique ('UNI') key columns.
*   Ensure the returned `TableChange` object:
    *   Has `getId()` equal to the input `TableId`.
    *   Has `getType()` equal to `TableChanges.TableChangeType.CREATE`.
    *   Contains a `Table` with correctly typed columns and primary key column names reflecting only 'PRI'-marked columns.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.