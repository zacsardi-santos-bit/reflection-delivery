Implement reusable task factory functions in the inspection taskbase package to streamline common log-processing patterns. Ensure these functions respect the framework's run-mode vs dry-run-mode distinction, and add test-utility methods to the history builder and timeline builder types.

*   Implement `NewLogFilterTask` in `pkg/core/inspection/taskbase/logfilter_task.go`:
    *   In run mode, apply the provided `LogFilterFunc` to each log from the source and return a slice containing only the logs for which the function returns true, preserving original order.
    *   In dry-run mode, return an empty slice without invoking the filter function.
    *   Define `LogFilterFunc` as a type alias for `func(ctx context.Context, log *log.Log) bool`.

*   Implement `NewLogGrouperTask` in `pkg/core/inspection/taskbase/loggroup_task.go`:
    *   In run mode, call the `LogGrouper` function on each log and place logs into the corresponding group in a `LogGroupMap`, preserving per-group log order.
    *   In dry-run mode, return an empty `LogGroupMap` without invoking the grouper function.
    *   Define `LogGroupMap` as a type alias for `map[string]*LogGroup`.
    *   Define `LogGrouper` as a type alias for `func(ctx context.Context, log *log.Log) string`.

*   Implement `NewFieldSetReadTask` in `pkg/core/inspection/taskbase/fieldsetread_task.go`:
    *   In run mode, apply each `FieldSetReader` to each log by calling `SetFieldSetReader` on the log, handling batches larger than the internal concurrency limit (at least 20 logs).
    *   In dry-run mode, do nothing and leave all log fieldsets unset.

*   Implement `NewHistoryModifierTask` in `pkg/core/inspection/taskbase/historymodifier_task.go`:
    *   In run mode, retrieve the `LogGroupMap` from the task referenced by `GroupedLogTask()`, process each log sequentially within its group, and flush each log's `ChangeSet` to the history `Builder` only on success.
    *   If `ModifyChangeSetFromLog` returns an error, do not flush the `ChangeSet` for that log but continue processing subsequent logs in the same group.
    *   Register all logs in the history builder's log list, even those that errored.
    *   In dry-run mode, make no history modifications; ensure history has zero timelines and zero log entries.

*   Update `pkg/model/history/builder.go`:
    *   Add method `DangerouslyGetRawHistory() *History` to return the raw `History` pointer accumulated by the builder for testing.

*   Update `pkg/model/history/timeline_builder.go`:
    *   Add method `GetClonedEvents() []ResourceEvent` to return a copy of the timeline's `ResourceEvent` slice for testing.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.