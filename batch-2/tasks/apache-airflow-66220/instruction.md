I'm working with the Elasticsearch provider in Apache Airflow and I need to add Polars DataFrame support for Elasticsearch SQL queries.

*   Must create a new utility module at providers/elasticsearch/src/airflow/providers/elasticsearch/utils/sql.py containing a read_sql_to_polars function, and a corresponding __init__.py to make the utils directory a proper Python package.

*   The read_sql_to_polars function must accept an Elasticsearch client as its first argument, a SQL query string as its second argument, and optional keyword arguments: params (default None), fetch_size (integer, default 1000), and max_rows (integer or None, default None), returning a polars.DataFrame.

*   The returned DataFrame must have column names taken from the 'name' field of the 'columns' metadata in the Elasticsearch SQL API response, with rows populated from the 'rows' field. Empty row results must return a DataFrame with the correct column schema and zero rows.

*   The function must implement cursor-based pagination: if the initial response includes a 'cursor' field, subsequent pages must be fetched using that cursor until no cursor appears in the response. Rows from all pages must be accumulated into the final DataFrame.

*   When max_rows is specified and the total accumulated rows reach or exceed that count, the result must be truncated to exactly max_rows rows and no further pages must be fetched.

*   After cursor-based pagination completes (i.e., a cursor was present in at least one response), the client's clear_cursor method must be called exactly once. The cursor value used for cleanup must be the last non-null cursor value seen during pagination.

*   When the entire result fits in a single page and no cursor is ever returned, the client's clear_cursor method must NOT be called.

*   When max_rows is satisfied within the first page before any pagination occurs (no cursor is used), the client's clear_cursor method must NOT be called.

*   The ElasticsearchSQLHook's method for fetching a DataFrame must be updated so that when the polars DataFrame type is requested, it no longer raises a not-implemented error but instead delegates to read_sql_to_polars, passing the Elasticsearch client, the SQL query, the query parameters, and any additional keyword arguments (such as fetch_size and max_rows).

*   The read_sql_to_polars function must be importable from the airflow.providers.elasticsearch.hooks.elasticsearch module namespace (i.e., it must be imported at the top level of that module).


*   Interface details: Type: Function
Name: read_sql_to_polars
Location: providers/elasticsearch/src/airflow/providers/elasticsearch/utils/sql.py
Signature: read_sql_to_polars(client, query, params=None, fetch_size=1000, max_rows=None) -> pl.DataFrame
Description: Executes an Elasticsearch SQL query using cursor-based pagination and returns the results as a Polars DataFrame. The client is an Elasticsearch client object. The query is a SQL string. params is an optional mapping or iterable of query parameters (default None). fetch_size is an integer controlling the batch size per page (default 1000). max_rows is an optional integer limiting the total rows returned (default None). The returned DataFrame has column names from the 'name' field of the 'columns' metadata in the initial response. When a cursor is present in a response, subsequent pages are fetched. After pagination, clear_cursor is called once on the last non-null cursor; it is not called if no cursor was ever used.

Type: Module
Name: utils
Location: providers/elasticsearch/src/airflow/providers/elasticsearch/utils/__init__.py
Description: Empty package init file that makes the utils directory a proper Python package importable as airflow.providers.elasticsearch.utils.

Type: Import
Name: read_sql_to_polars
Location: providers/elasticsearch/src/airflow/providers/elasticsearch/hooks/elasticsearch.py
Description: The read_sql_to_polars function from airflow.providers.elasticsearch.utils.sql must be imported at the top level of the hooks/elasticsearch.py module so it is accessible in that module's namespace (required for the hook's polars DataFrame retrieval path to delegate to it, and for the import to be patchable at airflow.providers.elasticsearch.hooks.elasticsearch.read_sql_to_polars).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.