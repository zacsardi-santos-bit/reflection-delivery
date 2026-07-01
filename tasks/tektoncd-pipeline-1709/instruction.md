Implement a label-based approach to associate pods with their parent TaskRun in the TaskRun reconciler. Ensure that pods are labeled correctly and modify the reconciler to use label queries instead of direct name lookups. Update the reconciler to handle TaskRun cancellation by deleting associated pods and managing errors appropriately.

*   Update the `getPod` function in `pkg/reconciler/taskrun/taskrun.go`:
    *   List pods in the TaskRun's namespace using the label selector "tekton.dev/taskRun=<taskrun-name>".
    *   Return the sentinel error `errNoPodForTaskRun` if zero pods match.
    *   Return an error if more than one pod matches.
    *   Propagate any errors from the list operation.

*   Ensure pods created by the TaskRun reconciler carry the label `tekton.dev/taskRun: <taskrun-name>`.

*   Modify the TaskRun reconciler to handle cancellation:
    *   Set the TaskRun's succeeded condition to: Type=ConditionSucceeded, Status=ConditionFalse, Reason='TaskRunCancelled', Message='TaskRun "<name>" was cancelled'.
    *   Delete the associated pod if found via `getPod`. Ensure a subsequent GET returns a not-found error.
    *   If `getPod` returns `errNoPodForTaskRun`, update the condition and return without error.
    *   If `getPod` returns any other error, propagate that error from the Reconcile method.

*   Update the `Recorder` struct in `pkg/reconciler/taskrun/metrics.go`:
    *   Add an unexported boolean field named `initialized`.
    *   Ensure `DurationAndCount`, `RunningTaskRuns`, and `RecordPodLatency` return a non-nil error when `initialized` is false.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.