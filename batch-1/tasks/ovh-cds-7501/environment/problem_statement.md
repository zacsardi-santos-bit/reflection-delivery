## Description

When a workflow job has a matrix expansion strategy and is supposed to be skipped because its upstream dependency was skipped, the workflow engine attempts to interpolate the matrix template expressions anyway. If any of those expressions are invalid or rely on context that is not available at that point, the engine fails with an error instead of simply skipping the job.

The same issue applies when a job references a job template: even if the job should be skipped, the system tries to resolve the template, which can cause unexpected failures.

## Expected Behavior

- If a job is being skipped (because its dependency was skipped or its condition evaluates to false), and that job has a matrix strategy, the matrix must NOT be expanded. The job should be recorded as a single skipped entry without any evaluation of matrix expressions.
- If a job is being skipped and it references an external job template, the template must NOT be resolved. The job should be recorded as a single skipped entry.
- If a job has a matrix strategy with an empty list (producing no combinations), the job should be skipped and recorded as a single skipped entry.
- In all of the above cases, any downstream jobs that depend on the skipped job should also be skipped.
- When all jobs in a workflow run are skipped, the overall workflow run should be finalized with a "Skipped" status.

## Why This Matters

Currently, a workflow with jobs that use dynamic matrix expressions (e.g., computing the matrix values from context) can fail at runtime when one of those jobs should simply be skipped. This produces confusing errors and prevents workflows from completing gracefully. Operators expect that a skipped job — regardless of its configuration — propagates the skip cleanly to downstream jobs and terminates the workflow without errors.
