Implement a method to ensure that the list of scheduled jobs is returned in a consistent and deterministic order. This will allow for reliable comparisons and deterministic logic based on the job list.

*   Update the `Jobs()` method in `scheduler.go` to ensure consistent ordering:
    *   The method signature is `Jobs() []Job`.
    *   Ensure that the order of jobs returned is stable and deterministic across multiple calls.
    *   Two consecutive calls to `Jobs()` without any changes to the scheduler should return identical slices.

*   Ensure that the ordering logic works correctly even when there are 21 or more jobs in the scheduler.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.