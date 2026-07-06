I'm working on adding an LLM-powered schema comparison feature to the Apache Airflow common AI provider.

*   LLMSchemaCompareOperator must raise ValueError with a message matching "Provide at least one of 'data_sources' or 'db_conn_ids'" when neither data_sources nor db_conn_ids is provided.

*   LLMSchemaCompareOperator must raise ValueError with a message matching "'table_names' is required when using 'db_conn_ids'" when db_conn_ids is provided without table_names.

*   LLMSchemaCompareOperator must raise ValueError with a message matching 'at-least two combinations' when the total number of db_conn_ids plus data_sources is fewer than 2.

*   The default value of context_strategy must be 'full' when not explicitly specified at construction time.

*   LLMSchemaCompareOperator accepts optional parameters: context_strategy (Literal['basic','full']), system_prompt (str), and agent_params (dict). These must be stored as instance attributes with those exact names.

*   LLMSchemaCompareOperator._get_db_hook(conn_id) must return the DbApiHook for the connection. If the resolved hook is not an instance of DbApiHook, it must raise ValueError with a message matching 'does not provide a DbApiHook'.

*   LLMSchemaCompareOperator._is_dbapi_connection(conn_id) must return True when the connection resolves to a DbApiHook and False when it resolves to a different hook type. It must only suppress AirflowException and ValueError internally; any other exception type (e.g. a plain Exception) must propagate to the caller unchanged.

*   LLMSchemaCompareOperator._introspect_db_schema(hook, table_name) must return an empty string '' when get_table_schema returns no columns. For context_strategy='full', the returned string must include: a 'Columns: {name} {type}, ...' line, 'Primary Key: {col1}, {col2}' when primary keys exist, 'Foreign Key: ({constrained_cols}) -> {referred_table}({referred_cols})' for each foreign key, and 'Index: {name} ({col_names})' for each index. For context_strategy='basic', the returned string must start with 'Columns:' and must not contain 'Primary Key', 'Foreign Key', or 'Index'.

*   LLMSchemaCompareOperator._introspect_schema_from_datafusion(ds_config) must call _df_engine.register_datasource(ds_config) and _df_engine.get_schema(ds_config.table_name), then return a string containing 'Source: {conn_id}', 'Format: ({format})', 'Table: {table_name}', and 'Columns: {schema_text}'. If the DataFusion provider is not installed, accessing _df_engine must raise AirflowOptionalProviderFeatureException.

*   LLMSchemaCompareOperator._build_schema_context() must produce a string that, for each db_conn_id, includes a section containing 'Source: {conn_id} ({dialect_name})' and 'Table: {table_name}'. It must call _introspect_db_schema for each DB connection and _introspect_datasource_schema for each data source.

*   LLMSchemaCompareOperator._build_system_prompt(schema_context) must return a string that contains: the substring 'You are a database schema comparison expert.'; the substring 'Schemas to compare:\n\n{schema_context}'; and when system_prompt is set, the substring 'Additional instructions:\n{system_prompt}'.

*   LLMSchemaCompareOperator.execute(context) must call _build_schema_context(), then _build_system_prompt(schema_context), then llm_hook.create_agent(output_type=SchemaCompareResult, instructions=<system_prompt_string>, **agent_params), then agent.run_sync(self.prompt). It must return the result as a dict with keys 'compatible', 'mismatches', and 'summary'.

*   SchemaCompareResult must be a Pydantic model with fields: compatible (bool), mismatches (list), and summary (str). Calling model_dump() must return a dict with exactly those keys.

*   _LLMSchemaCompareDecoratedOperator must have a class-level attribute custom_operator_name equal to the string '@task.llm_schema_compare'.

*   _LLMSchemaCompareDecoratedOperator.execute(context) must call the python_callable with resolved op_kwargs to obtain the prompt. If the callable returns a non-string, an empty string, a whitespace-only string, or None, it must raise TypeError with a message matching 'non-empty string'. When valid, it must set self.prompt to the callable's return value before proceeding with schema comparison.


*   Interface details: Type: Class
Name: LLMSchemaCompareOperator
Location: providers/common/ai/src/airflow/providers/common/ai/operators/llm_schema_compare.py
Description: Airflow operator that compares schemas across multiple databases or data sources using an LLM. Accepts sources via `db_conn_ids`+`table_names` or `data_sources` (list of DataSourceConfig). Constructor validates that at least two schema sources are provided.
Signature: __init__(self, *, data_sources: list[DataSourceConfig] | None = None, db_conn_ids: list[str] | None = None, table_names: list[str] | None = None, context_strategy: Literal["basic", "full"] = "full", system_prompt: str = ..., **kwargs) -> None

Method: _get_db_hook
Signature: _get_db_hook(conn_id: str) -> DbApiHook
Description: Resolves a connection ID to a DbApiHook. Static method. Raises ValueError (message matching "does not provide a DbApiHook") if the resolved hook is not a DbApiHook instance.

Method: _is_dbapi_connection
Signature: _is_dbapi_connection(conn_id: str) -> bool
Description: Returns True if the connection resolves to a DbApiHook; returns False if it resolves to a non-DbApiHook but does not raise. Only catches AirflowException and ValueError internally — any other exception type propagates to the caller.

Method: _introspect_db_schema
Signature: _introspect_db_schema(hook: DbApiHook, table_name: str) -> str
Description: Introspects schema from a database via a DbApiHook. Returns empty string "" when the table has no columns. For context_strategy="full": returns multi-line string with "Columns: {name} {type}, ...", then "Primary Key: {cols}", "Foreign Key: ({cols}) -> {ref_table}({ref_cols})", "Index: {name} ({cols})" lines as applicable. For context_strategy="basic": returns string starting with "Columns:" only, with no primary key, foreign key, or index lines.

Method: _introspect_schema_from_datafusion
Signature: _introspect_schema_from_datafusion(ds_config: DataSourceConfig) -> str
Description: Introspects schema using DataFusion. Calls _df_engine.register_datasource(ds_config) then _df_engine.get_schema(ds_config.table_name). Returns a string in the format: "Source: {conn_id} \nFormat: ({format})\nTable: {table_name}\nColumns: {schema_text}". Raises AirflowOptionalProviderFeatureException if the DataFusion provider is not installed.

Property: _df_engine
Description: Cached property that returns a DataFusionEngine instance. Raises AirflowOptionalProviderFeatureException (wrapping an ImportError) if DataFusion is not available.

Method: _introspect_datasource_schema
Signature: _introspect_datasource_schema(ds_config: DataSourceConfig) -> str
Description: Dispatches to _introspect_db_schema (via _get_db_hook) if the data source is a DbApiHook connection, otherwise delegates to _introspect_schema_from_datafusion.

Method: _build_schema_context
Signature: _build_schema_context() -> str
Description: Collects schema from all configured sources. For each db_conn_id calls _get_db_hook then _introspect_db_schema, producing a section labelled "Source: {conn_id} ({dialect_name})\nTable: {table_name}\n{schema_text}". For each data_source calls _introspect_datasource_schema. Joins all sections.

Method: _build_system_prompt
Signature: _build_system_prompt(schema_context: str) -> str
Description: Builds the LLM system prompt. The returned string must contain "You are a database schema comparison expert.", the section "Schemas to compare:\n\n{schema_context}", and when system_prompt is set, the section "Additional instructions:\n{system_prompt}".

Method: execute
Signature: execute(context: Context) -> dict[str, Any]
Description: Orchestrates schema comparison. Calls _build_schema_context(), then _build_system_prompt(schema_context), creates an LLM agent via llm_hook.create_agent(output_type=SchemaCompareResult, instructions=..., **agent_params), calls agent.run_sync(self.prompt), and returns result.output as a dict with keys "compatible", "mismatches", "summary".


Type: Class
Name: SchemaCompareResult
Location: providers/common/ai/src/airflow/providers/common/ai/operators/llm_schema_compare.py
Description: Pydantic model representing the structured output of a schema comparison. Used as the output_type for the LLM agent.
Signature: SchemaCompareResult(compatible: bool, mismatches: list, summary: str)


Type: Class
Name: _LLMSchemaCompareDecoratedOperator
Location: providers/common/ai/src/airflow/providers/common/ai/decorators/llm_schema_compare.py
Description: Internal operator class used by the @task.llm_schema_compare decorator. Inherits from both DecoratedOperator and LLMSchemaCompareOperator. The user's callable is called during execute() to produce the prompt string.
Signature: __init__(self, *, python_callable: Callable, op_args: Collection[Any] | None = None, op_kwargs: Mapping[str, Any] | None = None, **kwargs) -> None

Attribute: custom_operator_name
Value: "@task.llm_schema_compare"
Description: Class-level string attribute that identifies this operator in the Airflow UI and logs.

Method: execute
Signature: execute(context: Context) -> Any
Description: Resolves op_kwargs into the callable's arguments, calls python_callable to obtain the prompt string, validates that the return value is a non-empty, non-whitespace string (raises TypeError with message matching "non-empty string" otherwise), sets self.prompt to the return value, then delegates to LLMSchemaCompareOperator.execute.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.