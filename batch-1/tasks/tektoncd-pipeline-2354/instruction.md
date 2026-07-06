Implement a solution to ensure that Tekton pipeline tasks with conditions referencing other task results are correctly ordered and resolved. Update the dependency graph and result substitution logic to handle these cases.

*   Update the `Build` function in `pkg/reconciler/pipeline/dag/dag.go`:
    *   Detect task result references in condition parameters using expressions like `$(tasks.<taskName>.results.<resultName>)`.
    *   Register the referenced task as a dependency in the DAG. Ensure the graph reflects this by connecting nodes such that if task "x" has a condition with a param referencing task "a"'s result, then node "a" must appear in node "x"'s `Prev` list and node "x" must appear in node "a"'s `Next` list.

*   Modify the `ApplyTaskResults` function in `pkg/reconciler/pipelinerun/resources/apply.go`:
    *   Perform variable substitution on parameters within each `PipelineTaskCondition` found in the `ResolvedConditionChecks` of every resolved pipeline run task, in addition to the task's own parameters.
    *   Handle cases where a resolved pipeline run task's `PipelineTask` field is nil by skipping substitution on task params, but still substituting in condition params.
    *   Ensure that unresolved param references generate error messages identifying both the param name and the owning entity (condition ref or task name).

*   Ensure the internal function for converting pipeline task parameters to resolved result references also processes condition parameters from the pipeline task's conditions list.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.