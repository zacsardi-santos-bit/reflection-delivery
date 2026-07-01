Implement dynamic management of recurring scheduled jobs in River's job queue system. Allow developers to add, remove, and clear jobs at runtime without restarting the client process. Ensure these operations are thread-safe and handles are unique and non-reusable.

*   Define a `PeriodicJobHandle` type in `rivertype/river_type.go` as an integer for referencing jobs.
*   Update `PeriodicJobEnqueuer` in `internal/maintenance/periodic_job_enqueuer.go`:
    *   Change `periodicJobs` to `map[rivertype.PeriodicJobHandle]*PeriodicJob`.
    *   Implement `Add(periodicJob *PeriodicJob) rivertype.PeriodicJobHandle` to add a job, assign a handle, increment `nextHandle`, signal the run loop, and return the handle.
    *   Implement `AddMany(periodicJobs []*PeriodicJob) []rivertype.PeriodicJobHandle` to add multiple jobs atomically, signal the run loop, and return handles.
    *   Implement `Remove(periodicJobHandle rivertype.PeriodicJobHandle)` to remove a job by handle.
    *   Implement `RemoveMany(periodicJobHandles []rivertype.PeriodicJobHandle)` to remove multiple jobs by handles.
    *   Implement `Clear()` to empty `periodicJobs` without resetting `nextHandle`.
    *   Ensure `Add`, `AddMany`, `Remove`, `RemoveMany`, and `Clear` are thread-safe using a mutex.
    *   Use a buffered channel `recalculateNextRun` to signal job changes.
*   Support initial job configuration via `PeriodicJobEnqueuerConfig.PeriodicJobs`.
*   In `periodic_job.go`, create `PeriodicJobBundle` to wrap `PeriodicJobEnqueuer`:
    *   Methods: `Add`, `AddMany`, `Remove`, `RemoveMany`, `Clear`.
*   In `client.go`, implement `PeriodicJobs() *PeriodicJobBundle` to return the job bundle.
*   In `riverinternaltest`, modify `Logger` to enable debug logging based on `RIVER_DEBUG`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.