Implement an on-demand log rotation capability for the M3DB commit log by adding a method that allows immediate rotation of the current log file. Ensure that this method works correctly during concurrent writes and handles closed commit logs appropriately.

*   Update the CommitLog interface to include the RotateLogs() method.
    *   Location: `src/dbnode/persist/fs/commitlog/types.go`
    *   Signature: `RotateLogs() (File, error)`
    *   Description: Forces an immediate rotation of the active commit log file and returns a File value describing the newly-opened commit log file. Returns `errCommitLogClosed` if called on a closed commit log. Must be safe to call concurrently with Write operations.

*   Implement the RotateLogs() method to:
    *   Force an immediate rotation of the current commit log file.
    *   Return a File value with:
        *   Start field set to the aligned block start time.
        *   Duration field set to the configured block size.
        *   Index field as an int64 that increments by one for each rotation, starting from 1.
        *   FilePath field containing the string 'commitlog-0'.
    *   Ensure that after N calls to RotateLogs(), each preceded by at least one write, there are exactly N+1 commit log files on disk.
    *   Return the `errCommitLogClosed` error when called on a closed commit log.

*   Ensure RotateLogs() is safe to call concurrently with ongoing writes:
    *   Prevent panics or incorrect results when called from a goroutine that is continuously writing.

*   Preserve all data written before a RotateLogs() call, ensuring it remains readable after the commit log is closed.

*   Update the mock implementation of the CommitLog interface:
    *   Location: `commit_log_mock.go`
    *   Ensure the mock implements the RotateLogs() method to avoid compilation errors.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.