I'm working on a distributed Airflow setup where logs are stored remotely.

*   The remote log upload interface's upload method must accept an optional task instance parameter that defaults to None. Implementations must tolerate being called with ti=None without raising an error.

*   ElasticsearchRemoteLogIO.upload must accept ti=None as a keyword argument and return immediately without performing any upload work when ti is None.

*   OpensearchRemoteLogIO.upload must accept ti=None as a keyword argument and return immediately without performing any upload work when ti is None.

*   _configure_logging in airflow.sdk.execution_time.callback_supervisor must accept a client object as a second argument (after log_path) and call _remote_logging_conn (from airflow.sdk.execution_time.supervisor) with that client during logging setup.

*   CallbackSubprocess must have a _upload_logs() method that calls upload_to_remote (from airflow.sdk.log) with self.process_log as the sole argument (no ti argument). The method must use _remote_logging_conn (from airflow.sdk.execution_time.supervisor) as part of its execution context.

*   CallbackSubprocess.wait() must call self._upload_logs() after the subprocess finishes and before returning the exit code.

*   CallbackSubprocess._upload_logs() must catch all exceptions raised during upload and must not propagate them to the caller, ensuring the callback exit code is still returned.

*   When load_remote_log_handler (from airflow.sdk.log) returns None, _upload_logs() must complete without error. load_remote_log_handler must be called exactly once during each _upload_logs() invocation.

*   upload_to_remote in airflow.sdk.log must accept an optional ti parameter (defaulting to None) so it can be called without a task instance.


*   Interface details: Type: Method
Name: upload
Location: shared/logging/src/airflow_shared/logging/remote.py
Signature: upload(self, path: os.PathLike | str, ti: RuntimeTI | None = None) -> None
Description: Protocol method for uploading a log path to remote storage. The ti parameter must be optional with a default of None.

Type: Method
Name: upload
Location: providers/elasticsearch/src/airflow/providers/elasticsearch/log/es_task_handler.py
Signature: upload(self, path: os.PathLike | str, ti: RuntimeTI | None = None) -> None
Description: Uploads task logs to Elasticsearch. Must return immediately without any work when ti is None.

Type: Method
Name: upload
Location: providers/opensearch/src/airflow/providers/opensearch/log/os_task_handler.py
Signature: upload(self, path: os.PathLike | str, ti: RuntimeTI | None = None) -> None
Description: Uploads task logs to OpenSearch. Must return immediately without any work when ti is None.

Type: Function
Name: _configure_logging
Location: task-sdk/src/airflow/sdk/execution_time/callback_supervisor.py
Signature: _configure_logging(log_path: str, client: Client) -> tuple[FilteringBoundLogger, BinaryIO]
Description: Configures file-based logging for the callback subprocess. Must accept the active client as a second argument and call _remote_logging_conn (imported from airflow.sdk.execution_time.supervisor) with that client.

Type: Method
Name: _upload_logs
Location: task-sdk/src/airflow/sdk/execution_time/callback_supervisor.py
Signature: _upload_logs(self) -> None
Description: Uploads callback subprocess logs to remote storage after execution. Must call upload_to_remote (from airflow.sdk.log) with self.process_log as the sole positional argument (no ti argument). Must use _remote_logging_conn (from airflow.sdk.execution_time.supervisor) as part of the execution context. Must catch all exceptions to prevent propagation. Belongs to CallbackSubprocess class.

Type: Method
Name: wait
Location: task-sdk/src/airflow/sdk/execution_time/callback_supervisor.py
Signature: wait(self) -> int
Description: Waits for the callback subprocess to complete. Must call self._upload_logs() after the subprocess finishes and before returning the exit code. Belongs to CallbackSubprocess class.

Type: Function
Name: upload_to_remote
Location: task-sdk/src/airflow/sdk/log.py
Signature: upload_to_remote(logger: FilteringBoundLogger, ti: RuntimeTI | None = None)
Description: Uploads logs to remote storage. The ti parameter must be made optional with a default of None so it can be called without a task instance (as done by CallbackSubprocess._upload_logs).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.