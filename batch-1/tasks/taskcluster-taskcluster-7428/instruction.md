Implement changes to the generic worker to handle writable directory caches by transferring ownership rather than granting and revoking access. Ensure the worker logs the ownership transfer in multiuser mode and omits unnecessary log messages in non-multiuser mode.

*   Update the behavior for mounting writable directory caches:
    *   In multiuser mode:
        *   Transfer file ownership of the cached directory contents to the new task user.
        *   Emit a log message: "Updating ownership of files inside directory '<path>' from <previous_owner> to <task_user>" where <task_user> matches 'task_[0-9]*'.
    *   In insecure (non-multiuser) mode:
        *   Do not emit any ownership update log message.
*   Update the behavior for unmounting writable directory caches:
    *   In multiuser mode:
        *   Do not emit a 'Denying ... access to ...' log message.
        *   Remove any logic that revokes directory access at unmount time.
*   Remove the log message for granting directory access when mounting in multiuser mode. Replace this with the ownership transfer process.
*   Modify the `updateOwnership` function in `workers/generic-worker/mounts_insecure_test.go`:
    *   Ensure it returns an empty string slice, as no ownership update log message is expected in non-multiuser mode.
*   Modify the `updateOwnership` function in `workers/generic-worker/mounts_multiuser_test.go`:
    *   Ensure it returns a string slice containing a single regex pattern: "Updating ownership of files inside directory '.*<testName>' from .* to task_[0-9]*", matching the expected log message during mounting in multiuser mode.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.