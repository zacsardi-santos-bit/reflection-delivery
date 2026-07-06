I'm running into a problem with the Databricks operators in Airflow.

*   Each of DatabricksCreateJobsOperator, DatabricksSubmitRunOperator, and DatabricksRunNowOperator must expose a _get_merged_json() method that returns a dict representing the combined payload: the operator's json field merged with any separately-specified named parameters (named parameters take precedence over values from json). The returned dict is not yet normalised.

*   Payload validation (type checking via normalise_json_content, constraint checks such as conflicting arguments) must be deferred from the constructor to execute(). Constructing an operator with an invalid payload or conflicting parameters must NOT raise; the error must only surface when execute() is called.

*   execute() must NOT write the merged or normalised payload back into any operator template field (self.json, self.notebook_task, etc.). After execute() returns or raises, op.json and other named template fields must be identical to their pre-execute snapshots so that Airflow can re-render them on a retry.

*   DatabricksCreateJobsOperator.execute() must inject self.params entries as a list of {'name': key, 'default': value} dicts into the 'parameters' key of the payload sent to create_job or reset_job, without writing that list back into self.json.

*   DatabricksSubmitRunOperator.execute() must inject self.params entries as base_parameters into the appropriate task entry in the payload sent to submit_run, without writing base_parameters back into self.notebook_task or any other named template field.

*   DatabricksRunNowOperator.execute() must inject self.params entries as job_parameters in the payload sent to run_now, without writing that dict back into self.json.

*   When the json field resolves to a string (e.g. via a Jinja template), all three operators must parse the string into a dict at execution time. A string that is not valid JSON must be tried as a Python dict literal (the representation that Jinja produces when rendering a dict pulled from XCom). If neither JSON parsing nor Python literal evaluation succeeds, raise AirflowException with a message matching 'Databricks json payload string must be valid JSON'. If parsing succeeds but the result is not a mapping (e.g. a list), raise AirflowException with a message matching 'Databricks json payload must resolve to a mapping'.

*   DatabricksSubmitRunOperator.execute() must raise AirflowException with message 'git_source is required for dbt_task' before any Databricks API call when a dbt_task is present in the merged payload but no git_source is provided.

*   DatabricksSubmitRunOperator.execute() must raise AirflowException with a message matching "'pipeline_name' is not allowed in conjunction with 'pipeline_id'" before any Databricks API call when the merged payload contains a pipeline_task that specifies both pipeline_id and pipeline_name.

*   DatabricksSubmitRunOperator.execute() must validate the payload type (normalise_json_content) before calling find_pipeline_id_by_name or submit_run; if validation fails, neither API method must be called.

*   DatabricksRunNowOperator.execute() must raise AirflowException with message "Argument 'job_name' is not allowed with argument 'job_id'" before any Databricks API call when both job_id and job_name are present in the merged payload.

*   DatabricksRunNowOperator.execute() must validate the payload type (normalise_json_content) before instantiating DatabricksHook or calling find_job_id_by_name, cancel_all_runs, or run_now; if validation fails, DatabricksHook must not be instantiated and none of those API methods must be called.

*   DatabricksRunNowOperator.execute_complete() must obtain job_parameters from _get_merged_json() when building the repair_run payload, so that job_parameters supplied via the named argument (not inside json=) survive a defer/resume cycle.

*   The synchronous (non-deferrable) repair path must include job_parameters (from the merged json), run_id, and rerun_all_failed_tasks in the payload passed to repair_run.


*   Interface details: Type: Method
Name: _get_merged_json
Location: providers/databricks/airflow/providers/databricks/operators/databricks.py
Signature: _get_merged_json(self) -> dict
Description: Returns the merged JSON payload by combining the operator's `json` template field with any separately-specified named parameters (e.g. name, tasks, new_cluster, notebook_task, job_id, notebook_params, job_parameters). Named parameters take precedence over values from `json`. Must be implemented on DatabricksCreateJobsOperator, DatabricksSubmitRunOperator, and DatabricksRunNowOperator. The result is NOT normalised — callers pass it through normalise_json_content() as needed.

Type: Method
Name: execute
Location: providers/databricks/airflow/providers/databricks/operators/databricks.py
Signature: execute(self, context) -> Any
Description: Execution method for DatabricksCreateJobsOperator, DatabricksSubmitRunOperator, and DatabricksRunNowOperator. Must perform all payload validation (type checking, constraint checking) at execution time rather than at construction time. Must NOT mutate the operator's template fields (self.json, self.notebook_task, etc.) when building the merged payload. Must validate before instantiating the hook or making any API calls.

Notes on exact error messages that the tests assert:
- Invalid payload type: "Type <...> used for parameter json[...] is not a number or a string"
- JSON string that is not a mapping: "Databricks json payload must resolve to a mapping"
- Malformed JSON string: "Databricks json payload string must be valid JSON"
- dbt_task without git_source: "git_source is required for dbt_task"
- pipeline_task with both pipeline_id and pipeline_name: "'pipeline_name' is not allowed in conjunction with 'pipeline_id'"
- job_id and job_name both specified: "Argument 'job_name' is not allowed with argument 'job_id'"

Type: Method
Name: execute_complete
Location: providers/databricks/airflow/providers/databricks/operators/databricks.py
Signature: execute_complete(self, context, event) -> Any
Description: Resume method for DatabricksRunNowOperator after a deferred trigger fires. When a repair run is needed, must call repair_run with job_parameters sourced from _get_merged_json() (so that named job_parameters survive the defer/resume cycle rather than being read from a possibly-stale self.json).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.