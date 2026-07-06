## Description

The Airbyte sync operator has several gaps in its job lifecycle handling that cause incorrect behavior in production workflows.

**Issue 1: Cancelled jobs are silently treated as successes**
When an Airbyte job is cancelled remotely, the operator completes without raising an error. This makes it impossible to distinguish between a successfully completed sync and one that was cancelled mid-run. Cancelled jobs should be treated as failures and raise an error so that dependent tasks and failure callbacks are triggered appropriately.

**Issue 2: No hard task-level timeout with automatic job cancellation**
The operator has a wait timeout that controls how long it waits, but this does not cancel the underlying Airbyte job when exceeded. There is no mechanism to enforce a hard task-level execution deadline that automatically cancels the remote job before failing the task. When both a wait timeout and a hard execution deadline are configured, the earlier deadline should take precedence.

**Issue 3: Cancellation failures crash the task**
When the operator is killed (e.g., by a signal or scheduler intervention) and the cancel-job call fails, the exception propagates and can mask the real reason for the task's failure. Cancellation errors should be handled gracefully so the task fails for the right reason.

## Expected Behavior

- A cancelled Airbyte job should cause the task to fail with an appropriate error.
- A separate "execution timeout" concept should exist that, when exceeded, cancels the remote Airbyte job and then fails the task. If the job cancellation itself fails, the task should still fail due to the timeout (the cancellation error must not override the timeout error).
- When the operator is killed, any failure to cancel the remote Airbyte job should be logged as a warning rather than crashing the task, allowing any remaining cleanup to proceed.

## Why This Matters

These gaps lead to silent data pipeline issues — workflows continue past a cancelled or timed-out sync job as if it had succeeded. Proper lifecycle handling is essential for data reliability and correct downstream task behavior.
