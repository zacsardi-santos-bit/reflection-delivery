Implement a time-to-live (TTL) feature in the analysis controller to automatically delete completed analysis runs after a configurable retention period. Ensure the TTL policy can be configured per run and supports general and phase-specific retention windows.

Requirements:

* Implement the `maybeGarbageCollectAnalysisRun` method in `analysis/analysis.go`:
    * Signature: `(c *Controller) maybeGarbageCollectAnalysisRun(run *v1alpha1.AnalysisRun, logger *log.Entry) error`
    * Delete the run via the Kubernetes API if its TTL has been exceeded.
    * Return `nil` on success or if deletion fails with a not-found error.
    * Return the error for any other deletion failure.
    * Log "Trying to cleanup TTL exceeded analysis run" at info level before issuing the delete call.
    * Skip deletion and return `nil` if the run is not in a completed phase, has no `TTLStrategy`, already has a `DeletionTimestamp`, or `Status.CompletedAt` is `nil`.

* Modify the `reconcileAnalysisRun` method in `analysis/analysis.go`:
    * Call `maybeGarbageCollectAnalysisRun` for every analysis run already in a completed phase before returning early.
    * Set `run.Status.CompletedAt` to the current time using `timeutil.MetaNow()` when transitioning a run to a completed phase, if `CompletedAt` is not already set.
    * Log "Failed to garbage collect analysis run" at warning level if `maybeGarbageCollectAnalysisRun` returns a non-nil error.

* Define the `TTLStrategy` struct in `pkg/apis/rollouts/v1alpha1/analysis_types.go`:
    * Fields:
        * `SecondsAfterCompletion *int32` - Applies to all completed phases.
        * `SecondsAfterSuccess *int32` - Applies only to successful runs and overrides `SecondsAfterCompletion`.
        * `SecondsAfterFailure *int32` - Applies only to failed runs and overrides `SecondsAfterCompletion`.

* Update the `AnalysisRunSpec` type in `pkg/apis/rollouts/v1alpha1/analysis_types.go`:
    * Add an optional `TTLStrategy` field: `TTLStrategy *TTLStrategy`

* Update the `AnalysisRunStatus` type in `pkg/apis/rollouts/v1alpha1/analysis_types.go`:
    * Add an optional `CompletedAt` field: `CompletedAt *metav1.Time`

* Ensure TTL expiry is calculated as the number of seconds since `Status.CompletedAt` exceeds the applicable TTL value for the run to be eligible for deletion.

* Ensure phase-specific TTL settings only apply to runs whose phase matches:
    * `SecondsAfterSuccess` must not cause deletion of failed runs.
    * `SecondsAfterFailure` must not cause deletion of successful runs.
    * `SecondsAfterCompletion` remains the fallback when no matching phase-specific TTL is configured.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.