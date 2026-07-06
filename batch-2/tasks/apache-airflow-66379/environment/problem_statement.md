## Description

Callback subprocess logs are currently never uploaded to remote log storage after execution. Only task execution logs benefit from remote upload. This gap means callback logs are only available locally, which is problematic in distributed or ephemeral environments where local storage is not persistent.

Additionally, the remote log upload interface currently requires a task instance to always be provided, making it impossible to upload logs for processes (such as callbacks) that run outside the context of a specific task instance.

## Expected Behavior

- The remote log upload interface should allow the task instance parameter to be optional, so it can be called without providing a task instance.
- Both the Elasticsearch and OpenSearch remote log IO implementations should gracefully handle the case where no task instance is provided, returning early without performing any upload work in that case.
- After a callback subprocess finishes, its logs should be uploaded to remote storage (if remote logging is configured).
- If remote logging is not configured, the callback log upload should complete silently without error.
- If the upload fails for any reason (e.g., remote storage is unreachable), the error must be swallowed so that the callback's exit code is still returned correctly.
- The logging configuration step for callbacks should establish the remote logging connection using the active client.

## Why This Matters

Without this change, callback logs are invisible in systems that rely on remote log storage. Operators debugging callback failures in such environments cannot retrieve any log output. Making log upload optional (tolerant of no task instance) and adding it to the callback subprocess lifecycle closes this gap cleanly.
