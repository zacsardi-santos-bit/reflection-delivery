Implement a new database model in Apache Airflow to track time-based deadlines for DAG runs. This model should store details such as the DAG identifier, run identifier, deadline time, and a callback function with optional arguments. Ensure the model can save and retrieve deadline records from the database and provide a clear string representation of its data.

*   Implement the `Deadline` class in `airflow/models/deadline.py`.
    *   Inherit from SQLAlchemy's Base class and optionally from LoggingMixin.
    *   Map the class to the "deadline" database table.

*   Constructor (`__init__` method):
    *   Accept parameters: `deadline` (datetime, required), `callback` (str, required), `callback_kwargs` (dict or None, optional, defaults to None), `dag_id` (str or None, optional), and `dagrun_id` (int or None, optional).
    *   Store these parameters as attributes: `deadline`, `callback`, `callback_kwargs`, `dag_id`, and `dagrun_id`.
    *   If `callback_kwargs` is not provided, set the attribute to None.

*   Persistence:
    *   Implement `add_deadline(cls, deadline: Deadline, session: Session = NEW_SESSION) -> None` as a class method.
        *   Use the `@provide_session` decorator.
        *   Persist the `Deadline` instance to the database.
        *   Ensure the record can be retrieved with matching `dag_id`, `dagrun_id`, `deadline`, `callback`, and `callback_kwargs`.

*   String Representation (`__repr__` method):
    *   Format when both `dag_id` and `dagrun_id` are set and `callback_kwargs` is provided:
        *   '[DagRun Deadline] Dag: {dag_id} Run: {dagrun_id} needed by {deadline} or run: {callback}({json.dumps(callback_kwargs)})'.
    *   Format when `callback_kwargs` is None:
        *   '[DagRun Deadline] Dag: {dag_id} Run: {dagrun_id} needed by {deadline} or run: {callback}()'.

*   Ensure the `Deadline` class is importable from `airflow.models.deadline`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.