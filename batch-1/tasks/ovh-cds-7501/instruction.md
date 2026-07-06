Implement a mechanism to handle skipped jobs in a workflow automation system, ensuring that matrix expansions and template resolutions are bypassed when jobs are skipped. Propagate the skip status to downstream jobs and finalize the workflow with a "Skipped" status when applicable.

*   Detect when a job should be skipped due to:
    *   An upstream dependency being skipped.
    *   Its own condition evaluating to false.
*   For a job with a matrix strategy:
    *   Do not expand or interpolate the matrix if the job is skipped.
    *   Record the job as a single skipped entry, even if matrix expressions are dynamic.
*   For a job referencing a job template:
    *   Do not resolve or expand the template if the job is skipped.
    *   Record the job as a single skipped entry.
*   For a job with a matrix strategy that results in an empty list:
    *   Skip the job and record it as a single skipped entry.
*   Ensure all downstream jobs dependent on a skipped job are also skipped and recorded with a skipped status.
*   Set the workflow run status to "Skipped" when all jobs in the run are skipped.
*   Ensure the workflow run engine processes all skip scenarios correctly through multiple trigger invocations until reaching a terminal state, without errors.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.