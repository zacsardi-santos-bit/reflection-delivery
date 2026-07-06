I'm working with an Airbyte sync operator in Apache Airflow and I've found several problems with how it handles different job outcomes.

*   When AirbyteTriggerSyncOperator.execute_complete receives an event with status 'cancelled', it must raise RuntimeError with the message from the event.

*   When AirbyteTriggerSyncOperator.execute_complete receives an event with status 'timeout', it must attempt to cancel the Airbyte job by calling AirbyteHook.cancel_job with keyword argument job_id equal to the job_id from the event, and then raise RuntimeError with the message from the event.

*   When AirbyteTriggerSyncOperator.execute_complete handles a 'timeout' event and cancel_job raises an exception, the cancellation exception must be suppressed and the original RuntimeError from the timeout must still be raised (cancellation failure must not mask the timeout error).

*   AirbyteTriggerSyncOperator.on_kill must catch and suppress any exception raised by cancel_job, so that downstream logic (e.g. get_job_status) is still executed even when cancellation fails.

*   AirbyteSyncTrigger must accept an optional execution_deadline parameter (float or None, defaulting to None) in its constructor, distinct from the existing end_time parameter.

*   AirbyteSyncTrigger.serialize() must include execution_deadline in the returned kwargs dictionary.

*   When AirbyteSyncTrigger.run() is executing and execution_deadline is set and has elapsed, it must yield exactly one TriggerEvent with status 'timeout' and message 'Job run {job_id} has reached execution timeout.' and then stop.

*   AirbyteSyncTrigger.run() must use time.monotonic() (not time.time()) for all deadline and end_time comparisons.

*   When AirbyteSyncTrigger.run() detects that end_time has elapsed (without execution_deadline), it must yield exactly one TriggerEvent with status 'error' and message 'Job run {job_id} has not reached a terminal status after {end_time}s.' and then stop.

*   AirbyteSyncTrigger.run() must yield exactly one TriggerEvent for each terminal outcome (success, cancelled, error, timeout) and then stop — it must never yield more than one event per run.


*   Interface details: Type: Class
Name: AirbyteTriggerSyncOperator
Location: providers/airbyte/src/airflow/providers/airbyte/operators/airbyte.py
Description: Operator that triggers an Airbyte sync job. The execute_complete method handles deferred execution completion events, and on_kill handles graceful task termination.
Signature: execute_complete(self, context: dict, event: dict) -> None
  - When event["status"] == "cancelled": raises RuntimeError(event["message"])
  - When event["status"] == "timeout": calls AirbyteHook.cancel_job(job_id=event["job_id"]) (as keyword argument, using the AirbyteHook imported in airflow.providers.airbyte.operators.airbyte), suppresses any exception from cancel_job, then raises RuntimeError(event["message"])
  - When event["status"] == "error": raises RuntimeError(event["message"])
  - Otherwise (success): returns None
Signature: on_kill(self) -> None
  - Must catch and suppress any exception from the cancel_job call so that subsequent operations (e.g. get_job_status) are still executed

Type: Class
Name: AirbyteSyncTrigger
Location: providers/airbyte/src/airflow/providers/airbyte/triggers/airbyte.py
Description: Async trigger that polls Airbyte job status. Extended with an optional execution_deadline parameter.
Signature: __init__(self, job_id: int, conn_id: str, end_time: float, poll_interval: float, execution_deadline: float | None = None)
  - execution_deadline: optional absolute monotonic timestamp after which the task is considered timed out (distinct from end_time)
  - Stored as self.execution_deadline
Signature: serialize(self) -> tuple[str, dict]
  - Returns ("airflow.providers.airbyte.triggers.airbyte.AirbyteSyncTrigger", kwargs)
  - kwargs must include: "job_id", "conn_id", "end_time", "poll_interval", "execution_deadline"
Signature: run(self) -> AsyncGenerator[TriggerEvent, None]
  - Uses time.monotonic() for all time comparisons (not time.time())
  - When execution_deadline is set and execution_deadline <= time.monotonic(): yields TriggerEvent({"status": "timeout", "message": f"Job run {self.job_id} has reached execution timeout.", "job_id": self.job_id}) then returns
  - When end_time <= time.monotonic(): yields TriggerEvent({"status": "error", "message": f"Job run {self.job_id} has not reached a terminal status after {self.end_time}s.", "job_id": self.job_id}) then returns
  - For JobStatusEnum.SUCCEEDED: yields TriggerEvent({"status": "success", "message": f"Job run {self.job_id} has completed successfully.", "job_id": self.job_id}) then returns
  - For JobStatusEnum.CANCELLED: yields TriggerEvent({"status": "cancelled", "message": f"Job run {self.job_id} has been cancelled.", "job_id": self.job_id}) then returns
  - For JobStatusEnum.FAILED or unknown non-running status: yields TriggerEvent({"status": "error", "message": f"Job run {self.job_id} has failed.", "job_id": self.job_id}) then returns
  - On exception: yields TriggerEvent({"status": "error", "message": str(e), "job_id": self.job_id}) then returns
  - Must yield exactly one event per run (no duplicate events for terminal states)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.