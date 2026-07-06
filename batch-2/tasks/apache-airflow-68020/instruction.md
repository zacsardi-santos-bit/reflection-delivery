I'm working on improving the performance of DAG authorization checks in Airflow.

*   A new function clear_team_name_cache() must be added to the airflow.models.dag module and must be importable from it. It takes no arguments and returns None.

*   clear_team_name_cache() must clear all cached DAG team name entries so that subsequent calls to DagModel.get_team_name() re-query the database for fresh results.

*   DagModel.get_team_name(dag_id, session=session) must cache its result by dag_id. After the first call for a given dag_id, subsequent calls must return the cached value without performing a database query, even if the underlying data has changed.

*   After clear_team_name_cache() is called, DagModel.get_team_name() must perform a fresh database query on the next invocation and return the current (possibly different) value.

*   The bulk task instance delete endpoint must require exactly 4 base database queries (BASE_QUERY_COUNT = 4) before the per-task-instance deletion loop, reduced from the previous value of 5, as a result of the team name caching optimization.


*   Interface details: Type: Function
Name: clear_team_name_cache
Location: airflow-core/src/airflow/models/dag.py
Signature: clear_team_name_cache() -> None
Description: Clears all cached DAG team name results. Must be importable from airflow.models.dag. After calling this function, the next call to DagModel.get_team_name() for any dag_id will perform a fresh database query instead of returning a cached value.

Type: Method (with caching)
Name: get_team_name
Location: airflow-core/src/airflow/models/dag.py (on DagModel class)
Signature: get_team_name(dag_id: str, *, session: Session = NEW_SESSION) -> str | None
Description: Returns the team name associated with a DAG, or None if the DAG is not owned by a team. Results must be cached by dag_id so that repeated calls for the same dag_id do not re-query the database. The cache is shared across calls and persists until cleared by clear_team_name_cache().


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.