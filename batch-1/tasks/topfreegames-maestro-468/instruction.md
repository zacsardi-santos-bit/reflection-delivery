Implement a mechanism to ensure the runtime watcher worker exits gracefully when the event stream closes. Ensure that the worker stops all internal processes and cleans up resources without returning an error unless the initial setup fails.

*   Modify the `Start(ctx context.Context) error` method in the `RuntimeWatcherWorker` interface:
    *   Ensure it returns `nil` when the event channel is closed.
    *   Ensure all internal event-processing goroutines terminate before returning.
    *   Call `Stop()` on the runtime watcher before `Start()` returns.
    *   Return a non-nil error if the initial watch setup fails, and ensure `IsRunning()` returns `false` in this case.

*   Ensure proper handling of game room instance events:
    *   For instance-added and instance-updated events:
        *   Update the corresponding game room instance state.
        *   Log any processing errors without crashing or returning an error from `Start()`.
    *   For instance-deleted events:
        *   Clean up the corresponding game room state.
        *   Log any cleanup errors without crashing or returning an error from `Start()`.

*   Implement the `NewRuntimeWatcherWorker` function in `internal/core/workers/runtime_watcher_worker/runtime_watcher_worker.go`:
    *   Create and return a new runtime watcher worker that monitors game room instance events for the given scheduler.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.