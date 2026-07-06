I'm working on the OpenLineage integration in Apache Airflow and I need to extend how hook-level SQL lineage is tracked.

*   OperatorLineage must have a merge(other) method that returns a new OperatorLineage instance (never self or other). The returned instance has inputs equal to self.inputs concatenated with other.inputs, outputs equal to self.outputs concatenated with other.outputs, run_facets merged with self's values taking priority over other's (other's unique keys are included), and job_facets merged with the same self-priority semantics.

*   ExtractorManager.extract_metadata must accept task_instance as a required (non-default) 4th positional parameter.

*   ExtractorManager.get_hook_lineage must accept task_instance and task_instance_state keyword parameters. When the Hook Lineage Collector has SQL extras with key SqlJobHookLineageExtra.KEY.value that contain either a SQL statement or a job ID, it must call emit_lineage_from_sql_extras from airflow.providers.openlineage.utils.sql_hook_lineage with task_instance, the collected sql_extras list, and is_successful=True when task_instance_state is not FAILED (is_successful=False when state is FAILED). When no SQL extras are present, emit_lineage_from_sql_extras must not be called. When only SQL extras are present but no asset-based inputs/outputs, return None. When assets are collected, return OperatorLineage with those inputs and outputs.

*   get_openlineage_facets_with_sql in airflow.providers.openlineage.sqlparser must accept a new boolean use_connection parameter (default True). When use_connection=False, hook.get_sqlalchemy_engine() must not be called and sqlalchemy_engine=None must be passed to SQLParser.generate_openlineage_metadata_from_sql. When use_connection=True, hook.get_sqlalchemy_engine() is called. The function must return None if hook.get_openlineage_database_info raises AttributeError, and return None if hook.get_openlineage_database_dialect raises AttributeError.

*   A new module airflow.providers.openlineage.utils.sql_hook_lineage must be created containing _get_hook_conn_id, _resolve_namespace, _create_ol_event_pair, and emit_lineage_from_sql_extras.

*   _get_hook_conn_id(hook) must return hook.get_conn_id() if that method is callable, otherwise return getattr(hook, hook.conn_name_attr) if conn_name_attr exists, otherwise return None.

*   _resolve_namespace(hook, conn_id) must return None when conn_id is None. When conn_id is provided, it must call hook.get_connection(conn_id), then hook.get_openlineage_database_info(connection), then SQLParser.create_namespace(database_info) and return the result. Must return None on any exception during this chain.

*   _create_ol_event_pair must return a 2-tuple of RunEvent objects. The first has eventType=RunState.START and the second has eventType=RunState.COMPLETE when is_successful=True, or RunState.FAIL when is_successful=False. Both events must share the same Run object (which includes parent run facets from _get_parent_run_facet plus any provided run_facets) and Job object. Both events must include the provided inputs and outputs lists.

*   emit_lineage_from_sql_extras must always return None. It must skip extras with neither SQL text nor job ID without calling any helper or listener. For each processable extra, the query counter starts at 1 and skipped extras do not increment it. Job names must be formatted as '{dag_id}.{task_id}.query.{n}'. A jobType job facet with values jobType='QUERY', integration='AIRFLOW', processingType='BATCH' must always be included. An externalQuery run facet (ExternalQueryRunFacet with externalQueryId=job_id and source=namespace) must be added using setdefault semantics (parser-produced run_facets take priority) only when both job_id and namespace are available. When namespace is None, no externalQuery facet must be included. When SQL parsing raises an exception, fall back to a SQLJobFacet with SQLParser.normalize_sql(sql). Events are emitted via get_openlineage_listener().adapter.emit(event). If emission fails, a WARNING must be logged with the message 'Failed to emit OpenLineage events for SQL hook lineage' and the exception must not propagate.


*   Interface details: Type: Method
Name: merge
Location: providers/openlineage/src/airflow/providers/openlineage/extractors/base.py
Signature: merge(self, other: OperatorLineage) -> OperatorLineage
Description: Merges two OperatorLineage objects into a new one. Inputs and outputs are concatenated (self first, then other). Run facets and job facets are merged as dictionaries with self taking priority (other's unique keys are added, but shared keys keep self's values). Returns a new OperatorLineage instance (not self or other).

---

Type: Method
Name: get_hook_lineage
Location: providers/openlineage/src/airflow/providers/openlineage/extractors/manager.py
Signature: get_hook_lineage(self, task_instance=None, task_instance_state: TaskInstanceState | None = None) -> OperatorLineage | None
Description: Extracts hook-level lineage from the Hook Lineage Collector. Collects SQL extras (entries with key matching SqlJobHookLineageExtra.KEY.value) and calls emit_lineage_from_sql_extras with is_successful=(task_instance_state != TaskInstanceState.FAILED). Returns OperatorLineage with inputs/outputs when asset-based lineage is present, returns None when only SQL extras or nothing is collected. Does NOT call emit_lineage_from_sql_extras when no SQL extras are present.

---

Type: Method
Name: extract_metadata
Location: providers/openlineage/src/airflow/providers/openlineage/extractors/manager.py
Signature: extract_metadata(self, dagrun, task, task_instance_state: TaskInstanceState, task_instance) -> OperatorLineage
Description: task_instance is now a required (non-default) positional parameter.

---

Type: Function
Name: get_openlineage_facets_with_sql
Location: providers/openlineage/src/airflow/providers/openlineage/sqlparser.py
Signature: get_openlineage_facets_with_sql(hook: DbApiHook, sql: str | list[str], conn_id: str, database: str | None, use_connection: bool = True) -> OperatorLineage | None
Description: Parses SQL to produce OpenLineage facets. The new use_connection parameter controls whether hook.get_sqlalchemy_engine() is called. When use_connection=False, sqlalchemy_engine=None is passed to SQLParser.generate_openlineage_metadata_from_sql. Returns None if hook.get_openlineage_database_info raises AttributeError. Returns None if hook.get_openlineage_database_dialect raises AttributeError.

---

Type: Function
Name: _get_hook_conn_id
Location: providers/openlineage/src/airflow/providers/openlineage/utils/sql_hook_lineage.py
Signature: _get_hook_conn_id(hook) -> str | None
Description: Extracts the connection ID from a hook. Tries hook.get_conn_id() first (if callable). Falls back to getattr(hook, hook.conn_name_attr) if conn_name_attr is set. Returns None if neither is available.

---

Type: Function
Name: _resolve_namespace
Location: providers/openlineage/src/airflow/providers/openlineage/utils/sql_hook_lineage.py
Signature: _resolve_namespace(hook, conn_id: str | None) -> str | None
Description: Resolves the OpenLineage namespace from a hook and connection ID. Returns None immediately if conn_id is None. Calls hook.get_connection(conn_id), then hook.get_openlineage_database_info(connection), then SQLParser.create_namespace(database_info). Returns None on any exception during this chain.

---

Type: Function
Name: _create_ol_event_pair
Location: providers/openlineage/src/airflow/providers/openlineage/utils/sql_hook_lineage.py
Signature: _create_ol_event_pair(task_instance, job_name: str, is_successful: bool, inputs: list | None = None, outputs: list | None = None, run_facets: dict | None = None, job_facets: dict | None = None, event_time: datetime | None = None) -> tuple[RunEvent, RunEvent]
Description: Creates a START + COMPLETE/FAIL event pair for a single query child job. Returns a tuple of (start_event, end_event) where start_event.eventType == RunState.START, end_event.eventType == RunState.COMPLETE when is_successful=True, and RunState.FAIL when is_successful=False. Both events share the same Run (with parent run facets from _get_parent_run_facet and the provided run_facets) and Job objects. Both events include the provided inputs and outputs lists.

---

Type: Function
Name: emit_lineage_from_sql_extras
Location: providers/openlineage/src/airflow/providers/openlineage/utils/sql_hook_lineage.py
Signature: emit_lineage_from_sql_extras(task_instance, sql_extras: list, is_successful: bool = True) -> None
Description: Processes SQL hook lineage extras and emits per-query OpenLineage events. Always returns None. Skips extras that have neither SQL text (SqlJobHookLineageExtra.VALUE__SQL_STATEMENT.value) nor a job ID (SqlJobHookLineageExtra.VALUE__JOB_ID.value). For each processable extra (skipped extras do NOT increment the counter), the query counter starts at 1 and the job_name is formatted as "{task_instance.dag_id}.{task_instance.task_id}.query.{n}". Always adds a "jobType" job facet with job_type_job.JobTypeJobFacet(jobType="QUERY", integration="AIRFLOW", processingType="BATCH"). When both job_id and namespace are available, adds an ExternalQueryRunFacet(externalQueryId=job_id, source=namespace) under key "externalQuery" using setdefault (so parser-produced run_facets take priority). Falls back to SQLParser.normalize_sql(sql) in a SQLJobFacet when SQL parsing raises an exception. Emits events via get_openlineage_listener().adapter.emit(event). Catches all exceptions during emission and logs a WARNING with the message "Failed to emit OpenLineage events for SQL hook lineage".

CRITICAL IMPORT REQUIREMENT: The following names must be imported directly at the top level of the sql_hook_lineage module (not accessed via qualified attribute paths) so that test patching at the module level works: lineage_run_id, lineage_job_name, lineage_job_namespace, lineage_root_run_id, lineage_root_job_name, lineage_root_job_namespace, _get_logical_date (all from airflow.providers.openlineage.plugins.macros), generate_new_uuid (from openlineage.client.uuid), get_openlineage_facets_with_sql (from airflow.providers.openlineage.sqlparser), and get_openlineage_listener (from airflow.providers.openlineage.plugins.listener).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.