I'm hitting a file descriptor leak in the Airflow triggerer service.

*   When `TriggerRunnerSupervisor._handle_request` processes a `TriggerStateChanges` message with finished trigger IDs, it must call `upload_to_remote()` on the `TriggerLoggingFactory` associated with each finished trigger ID found in `logger_cache`.

*   Even if `upload_to_remote()` raises an exception during finished-trigger cleanup, `close()` must still be called on the same `TriggerLoggingFactory` — the file descriptor must not be leaked on failed uploads.

*   After processing a finished trigger, its ID must be removed from `logger_cache` regardless of whether `upload_to_remote()` raised an exception.

*   After processing a finished trigger, its ID must be removed from `running_triggers` regardless of whether `upload_to_remote()` raised an exception.


*   Interface details: Type: Class
Name: TriggerRunnerSupervisor
Location: airflow-core/src/airflow/jobs/triggerer_job_runner.py
Description: Supervisor process that manages running triggers. Has a `logger_cache` dict (maps trigger ID → TriggerLoggingFactory) and a `running_triggers` set. The `_handle_request` method processes incoming messages about trigger state changes.
Signature: _handle_request(msg, log, req_id) -> None

Type: Class
Name: TriggerLoggingFactory
Location: airflow-core/src/airflow/jobs/triggerer_job_runner.py
Description: Factory object associated with a trigger's log output. Must expose two methods: `upload_to_remote()` to push buffered log data to remote storage, and `close()` to release the underlying file descriptor.
Signature: upload_to_remote() -> None; close() -> None

Type: Class
Name: TriggerStateChanges
Location: airflow-core/src/airflow/utils/supervisor.py (or airflow-core/src/airflow/jobs/triggerer_job_runner.py messages module)
Description: Message type used to communicate trigger lifecycle events. Contains a `finished` field — a list of trigger IDs that have completed and require cleanup.
Signature: TriggerStateChanges(finished: list[int])


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.