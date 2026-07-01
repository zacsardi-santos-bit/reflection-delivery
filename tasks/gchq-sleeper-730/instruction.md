Refactor the CompactionJobStatusStore interface to reduce code duplication by implementing a single foundational streaming method. Update the interface to provide default implementations for various query patterns using this method. Develop an in-memory implementation for testing purposes, and update existing tests to use this new implementation.

*   Implement a new method in the CompactionJobStatusStore interface:
    *   Method: `streamAllJobs(String tableName)`
    *   Return type: `Stream<CompactionJobStatus>`
    *   Default implementation must throw `UnsupportedOperationException` with the message 'Instance has no compaction job status store'.

*   Update default methods in the CompactionJobStatusStore interface to utilize `streamAllJobs`:
    *   `getAllJobs(String tableName)`
        *   Collect results from `streamAllJobs(tableName)` into a list.
    *   `getUnfinishedJobs(String tableName)`
        *   Filter `streamAllJobs(tableName)` for jobs where `isFinished()` returns false, then collect to a list.
    *   `getJobsByTaskId(String tableName, String taskId)`
        *   Filter `streamAllJobs(tableName)` for jobs where `isTaskIdAssigned(taskId)` returns true, then collect to a list.
    *   `getJobsInTimePeriod(String tableName, Instant startTime, Instant endTime)`
        *   Filter `streamAllJobs(tableName)` using `isInPeriod(startTime, endTime)`, then collect to a list.
    *   Ensure `getJob(String jobId)` continues to throw `UnsupportedOperationException` with the message 'Instance has no compaction job status store'.

*   Develop an in-memory implementation of the CompactionJobStatusStore:
    *   Track job lifecycle events such as created, started, and finished.
    *   Support all querying operations using the new streaming method.
    *   Allow fixing a clock during testing for deterministic time-sensitive updates.

*   Update admin client compaction status report tests:
    *   Replace mock objects with the in-memory store to exercise real job lifecycle events and verify statuses through actual queries.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.