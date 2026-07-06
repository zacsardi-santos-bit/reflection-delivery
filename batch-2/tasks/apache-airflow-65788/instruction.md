I'm working on the OpenLineage provider for Apache Airflow and I'd like to add a proper public API so developers can emit lineage events directly from their custom task code.

*   A new public API module must be created that exposes is_openlineage_active() and emit(event), importable from airflow.providers.openlineage.api. The module delegates to an internal core submodule at airflow.providers.openlineage.api.core.

*   is_openlineage_active() must return True only when OpenLineage is not disabled AND a listener is present (get_openlineage_listener() returns non-None); it must return False when disabled or when the listener is None.

*   emit(event) must forward the event to listener.adapter.emit(event); when OpenLineage is disabled, it must be a no-op and must not invoke get_openlineage_listener().

*   emit_dataset_lineage must be importable from airflow.providers.openlineage.api.datasets. It must emit a single RunEvent with RunState.RUNNING when OpenLineage is active. The event's job name must be '{dag_id}.{task_id}', jobType facet must have jobType='TASK' and integration='AIRFLOW'. Run and job facets from the Airflow context (nominalTime from data_interval_start/end, ownership from task.owner, tags from dag.tags) must be included.

*   emit_dataset_lineage must propagate root parent run information from dag_run.conf['openlineage'] when the keys 'rootParentRunId', 'rootParentJobNamespace', and 'rootParentJobName' are present; otherwise the parent's run is used as the root.

*   In emit_dataset_lineage, user-supplied additional_run_facets and additional_job_facets must be merged into the event, but internal facets (such as 'parent' and 'jobType') must take priority and cannot be overridden by user-supplied values. Non-conflicting user-supplied facets must be preserved.

*   emit_dataset_lineage must raise ValueError (with 'inputs' in the message) when raise_on_error=True and both inputs and outputs are empty or None. It must raise TypeError when raise_on_error=True and any item in inputs or outputs is not a Dataset instance. When raise_on_error=False (default), these errors must be caught, no event emitted, and a WARNING log message 'emit_dataset_lineage raised an error' must be written.

*   emit_dataset_lineage must be a no-op when OpenLineage is disabled or when get_openlineage_listener() returns None. When task_instance is not provided, it must be resolved from the current Airflow execution context via get_task_instance_from_context().

*   emit_query_lineage must be importable from airflow.providers.openlineage.api.sql. It must emit a START RunEvent followed by a COMPLETE RunEvent (or FAIL RunEvent when is_successful=False). The two events must share the same run ID.

*   The job name produced by emit_query_lineage must follow the pattern '{dag_id}.{task_id}.manual_query.{counter}', where counter is a string returned by next_query_counter_from_context() and increments with each call within the same execution context. Each new task context restarts the counter at 1.

*   emit_query_lineage must attach an externalQuery run facet with externalQueryId=query_id and source=query_source_namespace. The parent run facet must reference the task run as parent; the root must resolve to the DAG run unless root information is present in dag_run.conf. The job type facet must have integration='AIRFLOW' and jobType='QUERY'.

*   When query_text is provided to emit_query_lineage, an SQL job facet with the query text must be included, and SQL-parsed inputs must be added to the event's inputs. When is_successful=False, the end event must be RunState.FAIL and include an errorMessage run facet with message=error_message. When start_time/end_time are provided, they must be used as the event times (in ISO format).

*   emit_query_lineage must support additional_run_facets and additional_job_facets that are merged into events (non-conflicting). It must be a no-op when OpenLineage is disabled. When task_instance is not provided, it must resolve from context via get_task_instance_from_context(). By default, errors during event construction and context resolution must be swallowed and logged at WARNING as 'emit_query_lineage raised an error'; when raise_on_error=True, errors must propagate.

*   get_dag_run_dag_and_task_from_ti(ti) must return a 3-tuple (dag_run, dag, task). On Airflow 3+, it must call ti.get_template_context() and extract 'dag_run', 'dag', and 'task'. On Airflow 2, it must read ti.dag_run, ti.task.dag, and ti.task directly without calling get_template_context().

*   build_task_event_run_facets must compose a dict of run facets merging: user-provided, parent, airflow, debug, processing_engine, and nominalTime sections. The nominalTime facet must use dag_run.data_interval_start/end and must be omitted when data_interval_start is None. get_task_parent_run_facet must be called with parent_run_id=parent_run_id, parent_job_name=dag.dag_id, and dr_conf=dag_run.conf. Internal facets must win over additional_run_facets; non-conflicting additional facets must be included.

*   build_task_event_job_facets must return a dict containing a jobType facet (always present, jobType='TASK'). It must add a documentation facet preferring task doc over dag doc. It must add an ownership facet using task.owner; when task.owner is 'airflow' (the Airflow default owner), it must fall back to dag.owner; ownership facet must be omitted when the resolved owner is empty. It must add a tags facet from dag.tags sorted alphabetically. Internal facets must win over additional_job_facets.

*   next_query_counter_from_context() must increment the '_openlineage_manual_query_counter' key in the current Airflow execution context dict and return the counter as a string ('1', '2', '3', ...). Each distinct context dict maintains its own independent counter. When get_current_context() raises (no active context), the function must return a random 8-character string and log an INFO message containing 'OpenLineage encountered an error when retrieving query counter from context'.


*   Interface details: Type: Function
Name: is_openlineage_active
Location: providers/openlineage/src/airflow/providers/openlineage/api/core.py (re-exported from api/__init__.py)
Signature: is_openlineage_active() -> bool
Description: Returns True when OpenLineage is enabled and a listener is available; returns False otherwise. Tests mock conf.is_disabled and get_openlineage_listener at "airflow.providers.openlineage.api.core".

Type: Function
Name: emit
Location: providers/openlineage/src/airflow/providers/openlineage/api/core.py (re-exported from api/__init__.py)
Signature: emit(event) -> None
Description: Forwards the given event to the OpenLineage adapter. Is a no-op when OpenLineage is disabled; does not invoke get_openlineage_listener() in that case.

Type: Function
Name: emit_dataset_lineage
Location: providers/openlineage/src/airflow/providers/openlineage/api/datasets.py (re-exported from api/__init__.py)
Signature: emit_dataset_lineage(*, inputs=None, outputs=None, task_instance=None, additional_run_facets=None, additional_job_facets=None, raise_on_error=False) -> None
Description: Emits a RUNNING RunEvent for the given datasets. Resolves task_instance from context via get_task_instance_from_context() when not provided. Raises ValueError (with "inputs" in the message) if both inputs and outputs are empty and raise_on_error=True. Raises TypeError if any item in inputs/outputs is not a Dataset instance and raise_on_error=True. Swallows errors and logs a WARNING "emit_dataset_lineage raised an error" when raise_on_error=False (default). Is a no-op when OpenLineage is disabled or listener is absent. Tests mock get_task_instance_from_context at "airflow.providers.openlineage.api.datasets.get_task_instance_from_context".

Type: Function
Name: emit_query_lineage
Location: providers/openlineage/src/airflow/providers/openlineage/api/sql.py (re-exported from api/__init__.py)
Signature: emit_query_lineage(*, query_id=None, query_source_namespace=None, task_instance=None, query_text=None, is_successful=True, error_message=None, start_time=None, end_time=None, additional_run_facets=None, additional_job_facets=None, raise_on_error=False) -> None
Description: Emits a START + COMPLETE (or FAIL) RunEvent pair for a query. Job name format: "{dag_id}.{task_id}.manual_query.{counter}" where counter is obtained from next_query_counter_from_context(). Attaches an externalQuery run facet with query_id and query_source_namespace. When is_successful=False, the second event has RunState.FAIL with an errorMessage run facet (message=error_message). Uses start_time/end_time as eventTime when provided. Resolves task_instance from context via get_task_instance_from_context() when not provided. Swallows errors and logs WARNING "emit_query_lineage raised an error" by default; raises when raise_on_error=True. Is a no-op when OpenLineage is disabled. Tests mock _create_ol_event_pair at "airflow.providers.openlineage.api.sql._create_ol_event_pair" and get_task_instance_from_context at "airflow.providers.openlineage.api.sql.get_task_instance_from_context".

Type: Function
Name: _create_ol_event_pair
Location: providers/openlineage/src/airflow/providers/openlineage/utils/sql_hook_lineage.py (also imported into api/sql.py so patchable at "airflow.providers.openlineage.api.sql._create_ol_event_pair")
Signature: _create_ol_event_pair(*, task_instance, job_name, is_successful=True, inputs=None, outputs=None, run_facets=None, job_facets=None, start_event_time=None, end_event_time=None) -> tuple[RunEvent, RunEvent]
Description: Creates a (start, end) RunEvent pair. The start event has RunState.START and the end event has RunState.COMPLETE (or RunState.FAIL if is_successful=False). Both events share the same run ID. A jobType facet with jobType="QUERY" and integration="AIRFLOW" is added internally to job facets; user-supplied job_facets are merged with the internal jobType winning conflicts. start_event_time and end_event_time are used as eventTime when provided. Also patchable at "airflow.providers.openlineage.utils.sql_hook_lineage._create_ol_event_pair" by sql_hook_lineage tests.

Type: Function
Name: get_dag_run_dag_and_task_from_ti
Location: providers/openlineage/src/airflow/providers/openlineage/utils/utils.py
Signature: get_dag_run_dag_and_task_from_ti(task_instance) -> tuple
Description: Returns a (dag_run, dag, task) tuple. On Airflow 3+ (AIRFLOW_V_3_0_PLUS=True), calls task_instance.get_template_context() and returns context["dag_run"], context["dag"], context["task"]. On Airflow 2 (AIRFLOW_V_3_0_PLUS=False), returns task_instance.dag_run, task_instance.task.dag, task_instance.task without calling get_template_context().

Type: Function
Name: build_task_event_run_facets
Location: providers/openlineage/src/airflow/providers/openlineage/utils/utils.py
Signature: build_task_event_run_facets(*, task_instance, dag_run, dag, task, task_uuid, ti_state, parent_run_id, parent_job_name=None, dr_conf=None, additional_run_facets=None) -> dict
Description: Composes all run facets for a task event: user-provided, parent, airflow, debug, processing_engine, and nominalTime. All parameters are keyword-only. nominalTime facet is built from dag_run.data_interval_start/end and omitted when data_interval_start is None. get_task_parent_run_facet is called with parent_run_id=parent_run_id, parent_job_name=(parent_job_name if provided else dag.dag_id), dr_conf=(dr_conf if provided else dag_run.conf). Internal facets take priority over additional_run_facets; non-conflicting additional facets are preserved.

Type: Function
Name: build_task_event_job_facets
Location: providers/openlineage/src/airflow/providers/openlineage/utils/utils.py
Signature: build_task_event_job_facets(*, task, dag, additional_job_facets=None) -> dict
Description: Composes all job facets for a task event. All parameters are keyword-only. Always includes jobType (jobType="TASK", integration="AIRFLOW"). Adds documentation facet (task doc preferred over dag doc when both exist). Adds ownership facet using task.owner; when task.owner is "airflow" (the Airflow default), falls back to dag.owner; omits ownership when the resolved owner is empty. Adds tags facet from dag.tags sorted alphabetically; omits when empty. Internal facets (jobType etc.) take priority over additional_job_facets; non-conflicting additional facets are preserved.

Type: Function
Name: next_query_counter_from_context
Location: providers/openlineage/src/airflow/providers/openlineage/utils/utils.py
Signature: next_query_counter_from_context() -> str
Description: Returns the next sequential query counter as a string ("1", "2", "3", ...) by incrementing the "_openlineage_manual_query_counter" key in the current Airflow execution context dict. Counter is isolated per context dict instance. When get_current_context() raises (no active context), returns a random 8-character string and logs an INFO message containing "OpenLineage encountered an error when retrieving query counter from context".

Type: Module
Name: airflow.providers.openlineage.api.core
Location: providers/openlineage/src/airflow/providers/openlineage/api/core.py
Description: Internal implementation module for the public API. Provides is_openlineage_active() and emit(event). Tests mock "airflow.providers.openlineage.api.core.conf.is_disabled" and "airflow.providers.openlineage.api.core.get_openlineage_listener".

Type: Module
Name: airflow.providers.openlineage.api
Location: providers/openlineage/src/airflow/providers/openlineage/api/__init__.py
Description: Public API package for OpenLineage. Must export is_openlineage_active and emit (from api.core), emit_dataset_lineage (from api.datasets), and emit_query_lineage (from api.sql).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.