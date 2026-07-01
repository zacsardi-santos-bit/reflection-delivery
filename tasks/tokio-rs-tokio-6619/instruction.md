Promote the observability metrics for the number of worker threads and the number of currently alive tasks to stable status, making them accessible without the unstable feature flag. Ensure that metrics requiring experimental internals remain gated behind the unstable flag and reorganize the test files to clearly separate stable and unstable metrics.

*   Update the method `num_alive_tasks()` in `tokio/src/runtime/metrics/runtime.rs`:
    *   Ensure it is accessible without the `tokio_unstable` feature flag.
    *   Implement the method to return the current number of alive tasks in the runtime.
    *   Ensure the counter increases when a task is spawned and decreases when a task exits.

*   Ensure the method `num_workers()` in `tokio/src/runtime/metrics/runtime.rs`:
    *   Is already accessible without the `tokio_unstable` feature flag.
    *   Returns the number of worker threads (1 for current-thread runtime, 2 for a multi-thread runtime configured with 2 workers).

*   Modify the test file `tokio/tests/rt_metrics.rs`:
    *   Ensure it does not require the `tokio_unstable` feature to compile and run.
    *   Use the file-level cfg attribute to gate only on `feature = "full"`, `not(target_os = "wasi")`, and `target_has_atomic = "64"`.

*   Create a new test file `tokio/tests/rt_unstable_metrics.rs`:
    *   Gate it with cfg(all(feature = "full", tokio_unstable, not(target_os = "wasi"), target_has_atomic = "64")).
    *   Include all runtime metrics tests that require the `tokio_unstable` flag, such as blocking threads counts, worker park/noop/steal counts, poll counts, histograms, queue depths, budget yield counts, and IO driver fd counts.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.