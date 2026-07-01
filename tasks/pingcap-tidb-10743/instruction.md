Implement fault-tolerant behavior in the garbage collection worker's delete range process. Ensure that the process continues despite individual store failures and correctly logs errors without marking incomplete tasks as done. Apply the same resilience to the redo delete ranges process.

*   Update the `deleteRanges` method in `store/tikv/gcworker/gc_worker.go`:
    *   Ensure it does not abort or return an error when a delete range request fails for one or more stores.
    *   Log failures and continue processing remaining ranges.
    *   Return `nil` on completion, even if individual ranges failed.
    *   Keep failed ranges in the pending list for retry in subsequent GC cycles.

*   Update the `redoDeleteRanges` method in `store/tikv/gcworker/gc_worker.go`:
    *   Apply the same error-tolerance behavior as `deleteRanges`.
    *   Continue processing subsequent ranges when one fails.
    *   Return `nil` on completion, even if individual ranges failed.
    *   Leave failed ranges in the done list without deleting their records.

*   Modify the `doUnsafeDestroyRangeRequest` method in `store/tikv/gcworker/gc_worker.go`:
    *   Treat a nil response or a nil `UnsafeDestroyRange` field as an error.
    *   Detect and treat a non-empty error string in the `UnsafeDestroyRange` response as an error.
    *   Return a non-nil error if any store fails for a given range.
    *   Ensure requests are sent to all active stores for each delete-range task.

*   Ensure `getUpStores` in `store/tikv/gcworker/gc_worker.go`:
    *   Returns all currently active stores from the PD client.
    *   With a cluster bootstrapped with 3 stores, it must return exactly 3 stores.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.