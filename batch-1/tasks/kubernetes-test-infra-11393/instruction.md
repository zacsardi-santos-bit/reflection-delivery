Implement support for an exclusion list in the status reconciler to skip automatic job triggering for specified organizations or repositories. Ensure that status retirement and migration continue unaffected for these excluded entities.

*   Update the `Controller` struct in `prow/statusreconciler/controller.go`:
    *   Add a new field named `addedPresubmitBlacklist` of type `sets.String` from `k8s.io/apimachinery/pkg/util/sets`.

*   Modify the `NewController` function in `prow/statusreconciler/controller.go`:
    *   Update the function signature to include `addedPresubmitBlacklist sets.String` as the second parameter, positioned between `continueOnError` and `prowJobClient`.
    *   Assign the `addedPresubmitBlacklist` parameter to the `Controller`'s `addedPresubmitBlacklist` field.

*   Implement logic in the `triggerNewPresubmits` method (internal method on `*Controller`):
    *   During iteration over org/repo keys in the added presubmits map, check if the org (portion before "/") or the full org/repo string is present in `addedPresubmitBlacklist` using `sets.String.Has()`.
    *   If a match is found, skip triggering ProwJobs for that org/repo by continuing the loop without returning an error.
    *   Ensure that status retirement and migration operations are not affected by this exclusion check.

*   Ensure that when `addedPresubmitBlacklist` is empty (`sets.NewString()`), all reconciliation operations, including ProwJob triggering, status retirement, and status migration, proceed as normal without any changes to existing behavior.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.