I'm working on the Airflow scheduler and running into an issue with how it handles partitioned timetable DAGs.

*   The _create_dag_runs method of SchedulerJobRunner must check whether a DAG uses a partitioned timetable by inspecting the timetable_partitioned field on the DAG model.

*   When timetable_partitioned is True and next_dagrun_partition_key is None, the method must log an error at ERROR level with a message that contains exactly the text 'dag_model.next_dagrun_partition_key is None', and must skip further processing of that DAG without calling _get_current_dag.

*   When timetable_partitioned is True and next_dagrun_partition_key is set to a non-None value, the method must proceed with normal DAG run creation processing by calling _get_current_dag.


*   Interface details: Type: Class
Name: SchedulerJobRunner
Location: airflow-core/src/airflow/jobs/scheduler_job_runner.py
Description: The main scheduler job runner class. The _create_dag_runs method must be updated to handle partitioned timetable DAGs. When timetable_partitioned is True, it must check next_dagrun_partition_key before proceeding to call _get_current_dag.
Signature: _create_dag_runs(dag_models: Iterable, session: Session) -> None

The _create_dag_runs method internally calls _get_current_dag to retrieve a serialized DAG for processing. The new logic must ensure:
- If the DAG model's timetable_partitioned is True and next_dagrun_partition_key is None, the method must log an error at ERROR level containing "dag_model.next_dagrun_partition_key is None" and skip that DAG (continue to the next), so _get_current_dag is NOT called.
- If the DAG model's timetable_partitioned is True and next_dagrun_partition_key is not None, the method must proceed and call _get_current_dag.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.