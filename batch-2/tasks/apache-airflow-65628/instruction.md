I'm running into two related issues with the DAG run clear operation in Airflow.

*   The DAG run clear endpoint (POST /dags/{dag_id}/dagRuns/{dag_run_id}/clear) with dry_run=true must return task instances with all required fields fully populated, including dag_display_name, dag_run_id, dag_id, task_id, state, and rendered_fields. Previously, raw ORM objects were returned without the necessary relationship data (eager-loaded joins), causing these fields to be missing or null.

*   When dry_run=true and only_failed=true, the endpoint must return only task instances whose state is 'failed' or 'upstream_failed', each with all fields fully populated. The total_entries count must reflect only the failing task instances.

*   When dry_run=true and only_new=true, the endpoint must identify 'new' tasks using a TI-existence check: a task is considered new if it appears in the latest DAG version's task list but has no existing TaskInstance row for the given run_id. This replaces the previous DAG version comparison approach.

*   When dry_run=true and only_new=true and all tasks in the latest DAG version already have TaskInstances for the given run, the endpoint must return an empty task_instances list and total_entries=0.

*   When dry_run=false and only_new=true, the endpoint must create new TaskInstance rows in the database for every task that exists in the latest DAG version but has no TaskInstance for the given run_id. The response must include dag_run_id equal to the requested run ID.

*   After a non-dry-run only_new clear creates new TaskInstances, a subsequent dry_run only_new request for the same run must return total_entries=0 and an empty task_instances list, because the TI-existence check will now find existing rows for all tasks.


*   Interface details: NO INTERFACES NEEDED

The tests exercise the existing `POST /dags/{dag_id}/dagRuns/{dag_run_id}/clear` HTTP endpoint exclusively through a test client. No new functions, classes, or methods are introduced that the tests call by name. All implementation changes are inside the existing route handler function located in:

  airflow-core/src/airflow/api_fastapi/core_api/routes/public/dag_run.py

The route handler uses `eager_load_TI_and_TIH_for_validation` from `airflow.api_fastapi.common.db.task_instances` (an existing utility function) and `TaskInstanceState` from `airflow.utils.state` (an existing enum) — but neither is called directly by the tests.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.