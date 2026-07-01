Implement support for range queries in the Prometheus metric provider of Argo Rollouts. Add functionality to specify a start time, end time, and step interval for queries, allowing for dynamic time expressions. Ensure proper error handling for any parsing failures.

*   Update the `EvalTime` function in `utils/evaluate/evaluate.go`:
    *   Accept a time expression string.
    *   Return a valid `time.Time` and a nil error if the expression evaluates correctly.
    *   Return `time.Time{}` and a non-nil error if the expression is invalid or does not evaluate to a time value.

*   Define the `PrometheusRangeQueryArgs` struct in `pkg/apis/rollouts/v1alpha1/`:
    *   Include three string fields: `Start`, `End`, and `Step`.
    *   `Start` and `End` are time expressions evaluated by `EvalTime`.
    *   `Step` is a duration string parsed by `time.ParseDuration`.

*   Modify the `PrometheusMetric` struct in `pkg/apis/rollouts/v1alpha1/`:
    *   Add a field `RangeQuery` of type `*PrometheusRangeQueryArgs`.
    *   When `RangeQuery` is non-nil, trigger range query mode.

*   Implement range query execution in the provider's `Run` method:
    *   Issue a range query (`QueryRange`) using evaluated start and end times and parsed step duration.
    *   Serialize the result as a flat JSON array of all numeric values from all time series streams.

*   Handle errors in the `Run` method:
    *   If `Start` cannot be evaluated, return a measurement with `Phase=AnalysisPhaseError`, `Value=""`, and a message: "failed to parse rangeQuery.start as time: " followed by the error.
    *   If `End` cannot be evaluated, return a measurement with `Phase=AnalysisPhaseError`, `Value=""`, and a message: "failed to parse rangeQuery.end as time: " followed by the error.
    *   If `Step` cannot be parsed, return a measurement with `Phase=AnalysisPhaseError`, `Value=""`, and a message: "failed to parse rangeQuery.step as duration: " followed by the error.
    *   Ensure `StartedAt` and `FinishedAt` timestamps are set in all error cases.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.