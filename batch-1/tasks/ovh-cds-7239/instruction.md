Implement the function `craftWorkflowRunV2` to support the expansion of reusable job templates in CDS workflows. Ensure that when a job references a template, it is expanded inline, and all dependencies are correctly rewired. Handle potential duplicate job names gracefully and update the workflow status appropriately.

*   Implement `craftWorkflowRunV2(ctx context.Context, runID string) error` in `engine/api/v2_workflow_run_craft.go`.
    *   Detect jobs with the `From` field pointing to a workflow template.
    *   Replace the referencing job with all jobs defined in the template's specification.
    *   Rewire dependencies:
        *   Root jobs in the template (jobs with no internal dependencies) must inherit the needs of the referencing job.
        *   Jobs downstream of the referencing job must update their needs to the leaf jobs of the template (jobs with no successors within the template).
    *   Support chaining of templates:
        *   If a job references template A and another job referencing template B depends on it, ensure the root jobs of B inherit the leaf jobs of A.
        *   The final downstream job must depend on the leaf jobs of the last template in the chain.
    *   Calculate the total job count after expansion:
        *   Total jobs = (original job count) - (number of template-referencing jobs) + (sum of jobs from each expanded template).
    *   Set the workflow run status to `V2WorkflowRunStatusBuilding` upon successful expansion.
    *   Handle duplicate template references:
        *   If the same template is referenced more than once, causing duplicate job names, do not return an error.
        *   Set the workflow run status to `V2WorkflowRunStatusFail`.
        *   Record a `V2WorkflowRunInfo` entry with a message containing "already exist in the parent workflow".

*   Ensure the `From` field in `sdk.V2Job` (likely in `sdk/workflow_v2.go`) is used to identify the workflow template to expand. The format should be "projectKey/vcsName/repoName/templateName".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.