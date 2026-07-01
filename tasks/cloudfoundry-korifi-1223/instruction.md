Implement the necessary changes to ensure that when a task is created via the API, the response includes the required timestamp fields. Capture the task's creation time from the underlying resource and format it correctly in the API response.

*   Update the `TaskRecord` struct in `api/repositories/task_repository.go`:
    *   Add a `CreationTimestamp` field of type `time.Time`.
    *   Ensure this field captures the task's actual creation time from the underlying resource when `CreateTask` is called.
    *   The `CreationTimestamp` should be approximately equal to the current time (within one second).

*   Modify the HTTP response for a created task:
    *   Include a `created_at` JSON field with the task's creation timestamp.
    *   Include an `updated_at` JSON field with the same value as `created_at`.
    *   Format both fields as UTC ISO 8601 strings without fractional seconds, ending with a 'Z' (e.g., '2022-06-14T13:22:34Z').

*   Update the `TaskResponse` struct in `api/presenter/task.go`:
    *   Add two new string fields: `CreatedAt` and `UpdatedAt`.

*   Modify the `ForTask` function in `api/presenter/task.go`:
    *   Signature: `ForTask(responseTask repositories.TaskRecord, baseURL url.URL) TaskResponse`
    *   Convert `TaskRecord.CreationTimestamp` to a UTC ISO 8601 formatted string.
    *   Assign this formatted string to both `CreatedAt` and `UpdatedAt` in the returned `TaskResponse`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.