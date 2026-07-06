I'm working with the BigQuery IO manager in Dagster and I need to add support for configurable write modes.

*   A BigQueryWriteMode enumeration must be defined in dagster_gcp.bigquery.io_manager with at least three members: TRUNCATE (string value 'truncate'), REPLACE (string value 'replace'), and APPEND (string value 'append').

*   BigQueryClient must accept optional write_mode (BigQueryWriteMode, default BigQueryWriteMode.TRUNCATE) and optional gcp_credentials (string) constructor parameters, and expose them as accessible instance attributes.

*   BigQueryClient.delete_table_slice(context, table_slice, conn) must read context.resource_config['write_mode'] and execute TRUNCATE TABLE when the value is 'truncate', using backtick-quoted fully qualified table name in the form `project.dataset.table`.

*   BigQueryClient.delete_table_slice must execute DROP TABLE IF EXISTS with a backtick-quoted fully qualified table name when write_mode is 'replace'.

*   BigQueryClient.delete_table_slice must execute no query at all when write_mode is 'append'.

*   When table_slice has non-empty partition_dimensions with partitions, BigQueryClient.delete_table_slice must use legacy partition-scoped DELETE FROM logic regardless of write_mode — the query must NOT contain DROP TABLE.

*   BigQueryIOManager must accept a write_mode configuration parameter (string, default 'truncate') and a gcp_credentials configuration parameter.

*   When BigQueryIOManager yields for execution, it must construct a BigQueryClient with the configured write_mode as a BigQueryWriteMode value and pass it as the db_client keyword argument to DbIOManager; the default write_mode must be BigQueryWriteMode.TRUNCATE.

*   When BigQueryIOManager is configured with gcp_credentials, those credentials must be propagated to BigQueryClient and accessible as client.gcp_credentials.


*   Interface details: Type: Enum
Name: BigQueryWriteMode
Location: python_modules/libraries/dagster-gcp/dagster_gcp/bigquery/io_manager.py
Description: Enumeration of write modes supported by the BigQuery IO manager. Must have at least three members: TRUNCATE (string value "truncate"), REPLACE (string value "replace"), and APPEND (string value "append"). The default write mode is TRUNCATE.

Type: Class
Name: BigQueryClient
Location: python_modules/libraries/dagster-gcp/dagster_gcp/bigquery/io_manager.py
Description: Client class for BigQuery IO operations. Accepts optional `write_mode` (BigQueryWriteMode, default BigQueryWriteMode.TRUNCATE) and optional `gcp_credentials` (string) constructor parameters. Exposes `write_mode` and `gcp_credentials` as accessible attributes. Implements the `delete_table_slice(context, table_slice, conn)` method with the following behavior:
- If `table_slice.partition_dimensions` is non-empty (partitioned table): uses legacy DELETE FROM logic regardless of write mode
- If `context.resource_config["write_mode"]` is "truncate": executes `conn.query("TRUNCATE TABLE `{database}.{schema}.{table}`")`
- If `context.resource_config["write_mode"]` is "replace": executes `conn.query("DROP TABLE IF EXISTS `{database}.{schema}.{table}`")`
- If `context.resource_config["write_mode"]` is "append": executes no query at all
Signature: delete_table_slice(context: OutputContext, table_slice: TableSlice, conn) -> None

Type: Class
Name: BigQueryIOManager
Location: python_modules/libraries/dagster-gcp/dagster_gcp/bigquery/io_manager.py
Description: IO manager class for BigQuery. Must accept a `write_mode` config parameter (string, default "truncate") and a `gcp_credentials` config parameter. When yielded for execution (via `yield_for_execution`), must construct a BigQueryClient with the configured `write_mode` and `gcp_credentials` and pass it as the `db_client` keyword argument to DbIOManager.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.