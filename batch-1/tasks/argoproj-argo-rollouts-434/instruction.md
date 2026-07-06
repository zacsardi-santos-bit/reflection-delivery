Implement a feature to improve failure message visibility when an analysis run aborts a rollout. Generate descriptive messages for each metric assessment, explaining threshold violations, and propagate these messages to the rollout's status condition. Update utility functions to support this functionality.

*   Define a constant:
    *   `DefaultConsecutiveErrorLimit` in `utils/defaults/defaults.go` with type `int32` and value `4`.

*   Implement utility functions:
    *   `GetConsecutiveErrorLimitOrDefault` in `utils/defaults/defaults.go`:
        *   Accepts a pointer to `v1alpha1.Metric`.
        *   Returns `int32`.
        *   Returns `metric.ConsecutiveErrorLimit` if non-nil, otherwise returns `DefaultConsecutiveErrorLimit`.

*   Create a function for metric assessment:
    *   `assessMetricFailureInconclusiveOrError` in `analysis/analysis.go`:
        *   Accepts `v1alpha1.Metric` and `v1alpha1.MetricResult`.
        *   Returns `(v1alpha1.AnalysisPhase, string)`.
        *   Returns appropriate phase and message based on threshold violations:
            *   `AnalysisPhaseFailed` if `result.Failed > metric.FailureLimit`.
            *   `AnalysisPhaseInconclusive` if `result.Inconclusive > metric.InconclusiveLimit`.
            *   `AnalysisPhaseError` if `result.ConsecutiveError` exceeds the limit from `GetConsecutiveErrorLimitOrDefault`.
            *   Returns `("", "")` if no conditions are met.

*   Modify existing methods:
    *   Rename `asssessRunStatus` to `assessRunStatus` in `analysis/analysis.go`:
        *   Change return type to `(v1alpha1.AnalysisPhase, string)`.
        *   Return `(AnalysisPhaseRunning, "")` if metrics are incomplete.
        *   Return worst phase and message when all metrics are complete.
        *   Format message as: `"metric \"<metric-name>\" assessed <Phase> due to <reason>"`.
        *   Append `": \"Error Message: <result.Message>\""` if `result.Message` is non-empty.
    *   Update `reconcileAnalysisRun` to set `run.Status.Message` using the message from `assessRunStatus`.

*   Enhance rollout abort functionality:
    *   Update `AddAbort` method in `rollout/pause.go`:
        *   Accepts a `message` string parameter.
        *   Stores the message and uses it as the rollout progressing condition message if non-empty.

*   Refactor metric status assessment:
    *   Use `assessMetricFailureInconclusiveOrError` in `assessMetricStatus` to determine metric status.
    *   Ensure externally observable behavior remains unchanged.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.