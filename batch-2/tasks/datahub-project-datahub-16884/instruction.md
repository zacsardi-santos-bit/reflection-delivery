I'm working on improving the Teradata ingestion connector in DataHub and need help implementing several performance and correctness fixes.

*   TeradataConfig must include a new optional datetime field 'column_extraction_watermark' that defaults to None. If the supplied value is timezone-aware, it must be normalized to a naive UTC datetime (tzinfo stripped after converting to UTC) before storage.

*   TeradataConfig must include a new optional integer field 'column_extraction_days_back' that defaults to None. Setting both 'column_extraction_watermark' and 'column_extraction_days_back' at the same time must raise a validation error whose message matches 'mutually exclusive'.

*   TeradataConfig must include a new boolean field 'use_dbc_columns_for_views' that defaults to False.

*   TeradataConfig must include a new integer field 'request_timeout_ms' that defaults to 120000, and a new integer field 'connect_timeout_ms' that defaults to 30000.

*   TeradataSource must expose a '_tables_needing_column_extraction' attribute that is None when neither 'column_extraction_watermark' nor 'column_extraction_days_back' is configured.

*   When 'column_extraction_watermark' is set, TeradataSource.cache_tables_and_views() must populate '_tables_needing_column_extraction' as a set of (database, table) tuples. Tables with a last-alter timestamp strictly after the watermark must be included. Tables with a last-alter timestamp before the watermark must be excluded. Tables with a None last-alter timestamp must be included (conservative behavior).

*   When 'column_extraction_days_back' is set, TeradataSource.cache_tables_and_views() must query the Teradata server's current timestamp via engine.connect(), subtract the configured number of days to derive the effective watermark, strip any timezone info from the resulting datetime, and then apply the same inclusion/exclusion logic as for an explicit watermark.

*   A new module-level function 'optimized_get_columns' must exist in datahub.ingestion.source.sql.teradata. When called with 'tables_needing_extraction' as a non-None set that does not contain (schema, table_name), the function must return an empty list without invoking any dialect column-retrieval methods.

*   When 'optimized_get_columns' is called with 'tables_needing_extraction=None' (no watermark) or when the table is present in the extraction set, the function must proceed with column extraction by invoking the dialect's get_schema_columns.

*   When 'optimized_get_columns' is called for a view entry with 'use_dbc_columns_for_views=True', it must use the bulk dbc.ColumnsV path (get_schema_columns). If any column in the result has a null or whitespace-only ColumnType, it must fall back to the HELP-based path (_get_column_help). If all column types are present and non-empty, it must NOT call _get_column_help.

*   When 'optimized_get_columns' is called for a view with 'use_dbc_columns_for_views=False' (the default), it must always use the HELP-based path (_get_column_help) and must NOT call get_schema_columns.

*   The functions get_schema_columns, get_schema_pk_constraints, and get_schema_foreign_keys must each be decorated with an LRU cache whose maxsize is exactly 32. Calling get_schema_columns with three distinct schemas must result in three separate cache entries, and a repeated call for the first schema must be served from the cache (hit, not miss).

*   TeradataSource._make_lineage_queries() must generate database filter clauses using case-insensitive Teradata syntax: 'DefaultDatabase (NOT CASESPECIFIC) in (\'db\' (NOT CASESPECIFIC), ...)'. The old case-sensitive form 'DefaultDatabase in (...)' must not appear.

*   TeradataSource._make_lineage_queries() must scope the database filter to the databases discovered in '_tables_cache' that are allowed by 'database_pattern', excluding any databases in the EXCLUDED_DATABASES list (e.g., 'All'). If an explicit 'databases' list is configured, it must take precedence over the cache-derived list. If '_tables_cache' is empty, the generated query must not contain any 'DefaultDatabase in' clause.

*   TeradataSource._get_or_create_pooled_engine() must pass connect_args to create_engine containing 'request_timeout' and 'connect_timeout' as string values derived from 'request_timeout_ms' and 'connect_timeout_ms' respectively. The defaults must produce the strings '120000' and '30000'.


*   Interface details: Type: Function
Name: optimized_get_columns
Location: metadata-ingestion/src/datahub/ingestion/source/sql/teradata.py
Signature: optimized_get_columns(dialect, conn, table_name: str, schema: str, tables_cache: Dict[str, List[TeradataTable]], tables_needing_extraction: Optional[Set[Tuple[str, str]]] = None, use_dbc_columns_for_views: bool = False) -> List
Description: Optimized column extraction that skips tables not in the extraction set and supports bulk dbc.ColumnsV lookup for views. When tables_needing_extraction is not None and (schema, table_name) is not in it, returns [] immediately. When use_dbc_columns_for_views=True and the object is a view, uses get_schema_columns and falls back to dialect._get_column_help if any column has a null or whitespace-only ColumnType. When use_dbc_columns_for_views=False (default) and the object is a view, always calls dialect._get_column_help directly.

Type: Function
Name: get_schema_columns
Location: metadata-ingestion/src/datahub/ingestion/source/sql/teradata.py
Signature: get_schema_columns(...) -> Any
Description: Module-level function decorated with functools.lru_cache(maxsize=32). Must support cache_info() and cache_clear() methods. Called with positional arguments including a connection and schema name.

Type: Function
Name: get_schema_pk_constraints
Location: metadata-ingestion/src/datahub/ingestion/source/sql/teradata.py
Signature: get_schema_pk_constraints(...) -> Any
Description: Module-level function decorated with functools.lru_cache(maxsize=32). Must support cache_info() method.

Type: Function
Name: get_schema_foreign_keys
Location: metadata-ingestion/src/datahub/ingestion/source/sql/teradata.py
Signature: get_schema_foreign_keys(...) -> Any
Description: Module-level function decorated with functools.lru_cache(maxsize=32). Must support cache_info() method. This function must be importable from datahub.ingestion.source.sql.teradata.

Type: Class
Name: TeradataConfig
Location: metadata-ingestion/src/datahub/ingestion/source/sql/teradata.py
Description: Configuration class for the Teradata source. Must include the following new fields:
  - column_extraction_watermark: Optional[datetime] = None. A field-level validator must convert timezone-aware datetimes to naive UTC (strip tzinfo after converting to UTC). Setting both column_extraction_watermark and column_extraction_days_back must raise a pydantic ValidationError whose message matches the string "mutually exclusive".
  - column_extraction_days_back: Optional[int] = None. Mutually exclusive with column_extraction_watermark.
  - use_dbc_columns_for_views: bool = False
  - request_timeout_ms: int = 120000
  - connect_timeout_ms: int = 30000

Type: Class
Name: TeradataSource
Location: metadata-ingestion/src/datahub/ingestion/source/sql/teradata.py
Description: Main Teradata ingestion source class. Must include the following new or modified behaviors:
  - _tables_needing_column_extraction: Optional[Set[Tuple[str, str]]] attribute. Must be None when no column_extraction_watermark or column_extraction_days_back is configured.
  - cache_tables_and_views(): Must populate _tables_needing_column_extraction when a watermark is configured. For column_extraction_days_back, must query the server's CURRENT_TIMESTAMP via engine.connect() (using fetchone()), subtract the configured days, strip tzinfo, then apply watermark logic. Tables with alter_time >= watermark are included; tables with alter_time < watermark are excluded; tables with None alter_time are included.
  - _make_lineage_queries(): Must use case-insensitive Teradata syntax for database name filtering: "DefaultDatabase (NOT CASESPECIFIC) in ('db' (NOT CASESPECIFIC), ...)". Must scope to databases from _tables_cache filtered by database_pattern and excluding EXCLUDED_DATABASES. Must use explicit 'databases' config if set. Must omit any "DefaultDatabase in" clause when _tables_cache is empty.
  - _get_or_create_pooled_engine(): Must pass connect_args={"request_timeout": str(config.request_timeout_ms), "connect_timeout": str(config.connect_timeout_ms)} to create_engine. Defaults produce strings "120000" and "30000".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.