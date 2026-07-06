I'm working on adding a configurable size limit to the Airflow state store.

*   The PUT endpoint for the asset state store must reject values whose byte size exceeds the configured maximum (default 65535 bytes) with HTTP 422.

*   The PUT endpoint for the asset state store must accept values of any size when the maximum is disabled (configured to 0), returning HTTP 204.

*   The PUT endpoint for the task state store must reject values whose byte size exceeds the configured maximum (default 65535 bytes) with HTTP 422.

*   The PUT endpoint for the task state store must accept values of any size when the maximum is disabled (configured to 0), returning HTTP 204.

*   The PATCH endpoint for the task state store must reject values whose byte size exceeds the configured maximum with HTTP 422.

*   The PATCH endpoint for the task state store must accept values of any size when the maximum is disabled (configured to 0), returning HTTP 200.

*   The configuration option 'max_value_storage_bytes' in the '[state_store]' config section controls the maximum allowed value size in bytes; setting it to '0' disables the limit entirely.

*   The TaskStateStoreAccessor.set() method in airflow/sdk/execution_time/context.py must emit a log warning (via the module-level 'log' logger) when the serialized value exceeds max_value_storage_bytes; the warning message must contain the string 'max_value_storage_bytes'.

*   The TaskStateStoreAccessor.set() method must still proceed to send the value (not block or raise) even when the size limit is exceeded and a warning is emitted.

*   The AssetStateStoreAccessor.set() method in airflow/sdk/execution_time/context.py must emit a log warning (via the module-level 'log' logger) when the serialized value exceeds max_value_storage_bytes; the warning message must contain the string 'max_value_storage_bytes'.

*   The AssetStateStoreAccessor.set() method must still proceed to send the value even when the size limit is exceeded and a warning is emitted.


*   Interface details: Type: Class
Name: TaskStateStoreAccessor
Location: task-sdk/src/airflow/sdk/execution_time/context.py
Description: Accessor used by tasks to get/set/delete task-scoped state store values. The set() method must be updated to emit a log warning when the serialized value exceeds the configured max_value_storage_bytes limit. The warning must be emitted via the module-level `log` logger (log.warning), the warning message must contain the string "max_value_storage_bytes", and the send operation must still be called even when the warning fires.
Signature: set(key: str, value: Any, ...) -> None

Type: Class
Name: AssetStateStoreAccessor
Location: task-sdk/src/airflow/sdk/execution_time/context.py
Description: Accessor used by tasks to get/set/delete asset-scoped state store values. The set() method must be updated to emit a log warning when the serialized value exceeds the configured max_value_storage_bytes limit. The warning must be emitted via the module-level `log` logger (log.warning), the warning message must contain the string "max_value_storage_bytes", and the send operation must still be called even when the warning fires.
Signature: set(key: str, value: Any, ...) -> None

Configuration:
- Section: state_store
- Key: max_value_storage_bytes
- Default: 65535
- When set to 0: the size limit is disabled (no enforcement, no warning)

API Enforcement:
- PUT /api/public/asset-state-store/... (or equivalent) must validate value byte size against max_value_storage_bytes and return HTTP 422 if exceeded; return HTTP 204 on success
- PUT /api/public/task-state-store/... (or equivalent) must validate value byte size and return HTTP 422 if exceeded; return HTTP 204 on success
- PATCH /api/public/task-state-store/... (or equivalent) must validate value byte size and return HTTP 422 if exceeded; return HTTP 200 on success
- When max_value_storage_bytes is 0, the size check is skipped and all values are accepted


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.