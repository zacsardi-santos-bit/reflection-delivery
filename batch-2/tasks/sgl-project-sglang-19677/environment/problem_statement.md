## Description

The tensor comparator's diagnostic logging system currently treats all messages as uniform "warnings" with no distinction between conditions that indicate real problems versus those that are merely informational. For example, a note that the alignment strategy fell back to a default is given the same weight as an actual rank mismatch that corrupts results. This makes it hard for users to quickly identify which messages require action.

Additionally, there is currently no way to tell the comparator to tolerate specific named failures — you can allow named skips to be ignored when computing the exit code, but if a known-bad tensor fails comparison, the entire run exits with a failure even if everything else passed.

Finally, when a tensor's dimension metadata doesn't match its actual number of dimensions, the failure message is unclear and difficult to diagnose.

## Expected Behavior

- Log messages should be separated into two levels: errors (conditions that actually caused a problem) and informational notices (advisory messages that do not indicate failures).
- Users should be able to specify a regex pattern naming tensors whose failures should be tolerated, so the overall exit code can still be zero when only those named tensors fail.
- When dimension metadata is inconsistent with the actual tensor shape, a clear descriptive error should be raised that explains the mismatch, the actual shape, the names provided, and how to fix it.
- The existing "allow skipped" pattern argument should be renamed for clarity.

## Why This Matters

Without log level separation, operators can't easily filter noise from real failures. Without a "tolerate failure" pattern, CI pipelines that need to allow a few known-bad tensors must ignore all failures. Clearer dimension mismatch errors reduce debugging time.
