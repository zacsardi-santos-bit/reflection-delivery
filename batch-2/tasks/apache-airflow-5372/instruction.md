Implement properties in the `TaskInstance` class to access information about previous task runs. Ensure that these properties provide details about the immediately preceding run, the most recent successful run, and specific dates related to successful runs.

*   Update the `TaskInstance` class in `airflow/models/taskinstance.py` to include the following properties:
    *   `previous_ti`
        *   Signature: `previous_ti -> Optional[TaskInstance]`
        *   Returns the immediately preceding `TaskInstance` object for the task, regardless of its state.
        *   Returns `None` if there is no prior task instance.
        *   Must function correctly for all schedule interval types: cron strings, timedelta objects, and None (no schedule), with both catchup=True and catchup=False.
    *   `previous_ti_success`
        *   Signature: `previous_ti_success -> Optional[TaskInstance]`
        *   Returns the most recent preceding `TaskInstance` whose state is SUCCESS.
        *   Returns `None` if no successful predecessor exists.
        *   Must skip over failed or non-successful task instances.
        *   Must function correctly for all schedule interval types: cron strings, timedelta objects, and None (no schedule), with both catchup=True and catchup=False.
    *   `previous_execution_date_success`
        *   Signature: `previous_execution_date_success -> Optional[pendulum.datetime]`
        *   Returns the `execution_date` of the most recent preceding successful `TaskInstance`.
        *   Returns `None` if no successful predecessor exists.
    *   `previous_start_date_success`
        *   Signature: `previous_start_date_success -> Optional[pendulum.datetime]`
        *   Returns the `start_date` of the most recent preceding successful `TaskInstance`.
        *   Returns `None` if no successful predecessor exists.

*   Ensure that when a task instance has no predecessors (i.e., it is the first run), the properties `previous_ti`, `previous_ti_success`, `previous_execution_date_success`, and `previous_start_date_success` all return `None`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.