## Description

The operator that deletes an AgentCore Runtime currently fires off the delete request and returns immediately — there is no way to tell when the runtime has actually been removed. This makes it difficult to chain dependent tasks that should only run once the resource is fully gone.

## Expected Behavior

- The delete operator should support an option to wait for the deletion to complete before the task finishes.
- Waiting should be available both synchronously (polling until the resource disappears) and asynchronously (suspending the Airflow task and resuming only when deletion is confirmed).
- Users who don't need to wait should still be able to opt out and return immediately after submitting the request.
- If deletion ends up in an unexpected terminal state instead of cleanly disappearing, the operator should raise an error rather than silently reporting success.
- The underlying polling logic should treat the resource being no longer found as the success condition, continue retrying while the resource is in a deleting state, and fail when the resource enters any other terminal state.

## Why This Matters

Workflows that provision and teardown AgentCore Runtimes need to know when a runtime is truly gone before proceeding to the next step. Without completion tracking, downstream tasks may attempt to create or configure resources that depend on the deleted runtime being fully removed, leading to race conditions or unexpected failures.
