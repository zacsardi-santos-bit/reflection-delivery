I'm working with MLflow's Databricks tracing backend that uses a SQL warehouse to store and query traces.

*   The MLFLOW_SQL_WAREHOUSE_AUTO_START environment variable must be exported from mlflow/environment_variables.py as a boolean variable defaulting to True. When set to false, ensure_sql_warehouse_running must be a complete no-op — it must not check the warehouse state or call start_and_wait.

*   The MLFLOW_SQL_WAREHOUSE_AUTO_START_TIMEOUT_SECONDS environment variable must be exported from mlflow/environment_variables.py as an integer variable defaulting to 1200. Its value is used as the timeout (in seconds) passed to start_and_wait when starting a warehouse.

*   ensure_sql_warehouse_running must be defined in mlflow/utils/databricks_sql_warehouse.py. When called with a warehouse ID whose state is RUNNING, it must check the warehouse state via the SDK but must not call start_and_wait.

*   When ensure_sql_warehouse_running is called with a warehouse in a non-RUNNING state (e.g., STOPPED, STOPPING, STARTING), it must emit an info-level log message containing the warehouse ID and the state name, then call start_and_wait(warehouse_id, timeout=timedelta(seconds=N)) where N comes from MLFLOW_SQL_WAREHOUSE_AUTO_START_TIMEOUT_SECONDS (default 1200).

*   ensure_sql_warehouse_running must cache successful checks in a module-level dict named _verified_running, keyed by warehouse ID, using time-based expiry controlled by a module-level float _CACHE_TTL_SECONDS. A second call for the same warehouse within the TTL must not re-check the warehouse state. A second call after the TTL expires must re-check. Different warehouse IDs must have independent cache entries.

*   If start_and_wait raises a TimeoutError, ensure_sql_warehouse_running must raise MlflowException with a message that includes the warehouse ID and the string MLFLOW_SQL_WAREHOUSE_AUTO_START_TIMEOUT_SECONDS.

*   If start_and_wait raises any other exception, ensure_sql_warehouse_running must raise MlflowException with a message that includes the warehouse ID, the original error message, and the string MLFLOW_SQL_WAREHOUSE_AUTO_START.

*   _resolve_sql_warehouse_id on DatabricksTracingRestStore must accept an optional explicit warehouse ID string. If provided, the explicit value takes precedence over MLFLOW_TRACING_SQL_WAREHOUSE_ID. The resolved ID must be passed to ensure_sql_warehouse_running before being returned. If no warehouse ID is available from either source, the method must return None without calling ensure_sql_warehouse_running.

*   _append_sql_warehouse_id_param on DatabricksTracingRestStore must call _resolve_sql_warehouse_id to obtain the warehouse ID. If a warehouse ID is resolved it must append ?sql_warehouse_id=<wh_id> to the endpoint string and return the result. If no warehouse ID is resolved it must return the endpoint unchanged.

*   The following DatabricksTracingRestStore methods must trigger ensure_sql_warehouse_running (via _resolve_sql_warehouse_id or _append_sql_warehouse_id_param) when MLFLOW_TRACING_SQL_WAREHOUSE_ID is set: batch_get_traces, get_trace_info (for v4 trace IDs), search_traces (for UC schema locations), create_or_get_trace_location, set_experiment_trace_location, delete_trace_tag (for v4 trace IDs), create_assessment (for v4 trace IDs), get_assessment (for v4 trace IDs), delete_assessment (for v4 trace IDs).

*   The following DatabricksTracingRestStore methods must NOT trigger ensure_sql_warehouse_running even when MLFLOW_TRACING_SQL_WAREHOUSE_ID is set: search_traces (when model_id is provided, routing to /api/2.0 SearchUnifiedTraces), get_online_trace_details, log_spans, unset_experiment_trace_location.


*   Interface details: Type: Function
Name: ensure_sql_warehouse_running
Location: mlflow/utils/databricks_sql_warehouse.py
Signature: ensure_sql_warehouse_running(warehouse_id: str) -> None
Description: Checks whether the given SQL warehouse is in the RUNNING state and starts it if not. Is a no-op when MLFLOW_SQL_WAREHOUSE_AUTO_START is disabled. Uses a module-level cache to avoid repeated checks within the TTL window.

Type: Module Variable
Name: _verified_running
Location: mlflow/utils/databricks_sql_warehouse.py
Description: Module-level dict used as a cache mapping warehouse_id to the last-verified timestamp. Must be clearable (supports .clear()).

Type: Module Variable
Name: _CACHE_TTL_SECONDS
Location: mlflow/utils/databricks_sql_warehouse.py
Description: Module-level float controlling the TTL for the _verified_running cache. Entries older than this many seconds are considered stale and will trigger a re-check.

Type: Module Variable
Name: _logger
Location: mlflow/utils/databricks_sql_warehouse.py
Description: Module-level logger used to emit info-level messages when a warehouse is not in the RUNNING state and is being started. The log message must include the warehouse ID and the current state name.

Type: Function
Name: _get_workspace_client
Location: mlflow/utils/databricks_sql_warehouse.py
Signature: _get_workspace_client() -> WorkspaceClient
Description: Internal helper that returns a Databricks SDK WorkspaceClient instance. Must be defined at module level so it can be patched in tests.

Type: Method
Name: _resolve_sql_warehouse_id
Location: mlflow/store/tracking/databricks_rest_store.py (class: DatabricksTracingRestStore)
Signature: _resolve_sql_warehouse_id(explicit: str | None = None) -> str | None
Description: Returns the SQL warehouse ID to use for a tracing call. If an explicit ID is provided it takes precedence over the environment variable. Calls ensure_sql_warehouse_running with the resolved ID before returning. Returns None (without calling ensure_sql_warehouse_running) if no warehouse ID is available from either source.

Type: Method
Name: _append_sql_warehouse_id_param
Location: mlflow/store/tracking/databricks_rest_store.py (class: DatabricksTracingRestStore)
Signature: _append_sql_warehouse_id_param(endpoint: str) -> str
Description: Appends ?sql_warehouse_id=<wh_id> to the given endpoint string if a warehouse ID is configured (via _resolve_sql_warehouse_id). Returns the endpoint unchanged if no warehouse ID is available.

Type: Environment Variable
Name: MLFLOW_SQL_WAREHOUSE_AUTO_START
Location: mlflow/environment_variables.py
Description: Boolean environment variable. When set to false/False/0, ensure_sql_warehouse_running becomes a complete no-op (no state check, no start). Defaults to True.

Type: Environment Variable
Name: MLFLOW_SQL_WAREHOUSE_AUTO_START_TIMEOUT_SECONDS
Location: mlflow/environment_variables.py
Description: Integer environment variable controlling the maximum number of seconds to wait for a SQL warehouse to reach the RUNNING state. Defaults to 1200. Applied as the timeout argument to start_and_wait.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.