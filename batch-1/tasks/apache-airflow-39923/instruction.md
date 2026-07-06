Integrate AWS Glue Data Quality checks into your Airflow pipelines by implementing a hook, operators, a sensor, and a trigger. These components will enable you to create or update data quality rulesets, start evaluation runs, and monitor their completion within your DAGs.

*   Implement `GlueDataQualityHook` in `airflow/providers/amazon/aws/hooks/glue.py`:
    *   Ensure it is instantiable with no arguments, defaulting to `aws_conn_id='aws_default'`.
    *   Implement `has_data_quality_ruleset(name: str) -> bool` to return `True` if the ruleset exists, or `False` if an `EntityNotFoundException` is raised.
    *   Implement `validate_evaluation_run_results(evaluation_run_id: str, show_results: bool) -> None` to log the number of failed rules and raise an `AirflowException` if any rule fails.
    *   Register a custom waiter named `data_quality_ruleset_evaluation_run_complete` that succeeds for `SUCCESS_STATES` (at least 'SUCCEEDED') and raises a `WaiterError` for `FAILURE_STATES` (at least 'FAILED', 'STOPPED', 'STOPPING', 'TIMEOUT').

*   Implement `GlueDataQualityOperator` in `airflow/providers/amazon/aws/operators/glue.py`:
    *   Accept parameters: `name`, `ruleset`, `description`, `update_rule_set` (default `False`), and `data_quality_ruleset_kwargs`.
    *   Store `name` and `ruleset` as instance attributes.
    *   Implement `validate_inputs()` to ensure the ruleset starts with "Rules = [" and ends with "]", raising an `AttributeError` if not.
    *   Implement `execute(context: dict) -> None` to create or update rulesets, raising an `AirflowException` for `AlreadyExistsException` or `EntityNotFoundException`.

*   Implement `GlueDataQualityRuleSetEvaluationRunOperator` in `airflow/providers/amazon/aws/operators/glue.py`:
    *   Accept parameters: `datasource`, `role`, `rule_set_names`, `show_results`, `number_of_workers`, `timeout`, `rule_set_evaluation_run_kwargs`, `wait_for_completion`, and `deferrable`.
    *   Store `datasource`, `role`, and `rule_set_names` as instance attributes.
    *   Implement `validate_inputs()` to check all rulesets exist, raising an `AirflowException` if any are missing.
    *   Implement `execute(context: dict) -> str` to start evaluation runs, returning the `RunId` and handling `wait_for_completion` and `deferrable` modes.

*   Implement `GlueDataQualityRuleSetEvaluationRunSensor` in `airflow/providers/amazon/aws/sensors/glue.py`:
    *   Define class-level `FAILURE_STATES` and `SUCCESS_STATES`.
    *   Implement `poke(context: dict) -> bool` to handle run states, raising an `AirflowException` or `AirflowSkipException` for failures.
    *   Implement `execute_complete(context: dict, event: dict) -> None` to log completion or raise an `AirflowException` on failure.

*   Implement `GlueDataQualityRuleSetEvaluationRunCompleteTrigger` in `airflow/providers/amazon/aws/triggers/glue.py`:
    *   Implement `serialize() -> tuple[str, dict]` to return the classpath and evaluation_run_id.
    *   Implement `run() -> AsyncGenerator` to use the custom waiter and yield a `TriggerEvent` on success.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.