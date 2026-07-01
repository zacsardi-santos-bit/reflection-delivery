Implement the function `canBeRun` to determine if a workflow node run is eligible for re-triggering based on the status of its ancestor nodes. Ensure that the function checks the state of upstream nodes in the workflow graph before allowing a node to be re-run.

Requirements:
*   Implement the function `canBeRun` in the file `engine/api/workflow/dao_run.go`.
    *   The function must be package-private (unexported).
    *   Signature: `canBeRun(workflowRun *sdk.WorkflowRun, workflowNodeRun *sdk.WorkflowNodeRun) bool`
*   Return `false` if the `workflowNodeRun`'s status is not terminated (e.g., building, waiting).
    *   Use the existing SDK status utility to determine terminated statuses (e.g., success, failure).
*   Identify ancestor nodes by traversing the workflow trigger graph in `WorkflowRun.Workflow`.
    *   Use `WorkflowNodeID` from `workflowNodeRun` to locate the current node.
*   Return `false` if any ancestor node has a non-terminated status in `WorkflowRun.WorkflowNodeRuns`.
    *   Check for statuses like `sdk.StatusBuilding`.
*   Return `false` if any ancestor node run has an empty string status.
*   Return `false` if any ancestor node run has a 'never built' status (`sdk.StatusNeverBuilt`).
*   Return `true` if the `workflowNodeRun`'s status is terminated and all ancestor node runs have a terminated, non-empty, non-NeverBuilt status (e.g., `StatusSuccess`, `StatusFail`).
*   If the node corresponding to `workflowNodeRun` cannot be found in the workflow graph, or if it has no ancestors, return `true` (provided the node run's status is terminated).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.