## Description

When a pipeline task has a condition that references the output (result) of a previously completed task as a parameter value, two things are broken:

1. The pipeline's task execution ordering (dependency graph) does not recognize this result reference as an actual dependency. As a result, the pipeline may attempt to run the conditional task before the task it depends on has finished.

2. Even when the upstream task has completed, the condition's parameters still contain raw variable placeholder expressions instead of the actual resolved values. The substitution step that replaces those placeholders with real values only handles the task's own parameters, but not the parameters passed to conditions attached to that task.

## Expected Behavior

- If a condition attached to a pipeline task uses a result from another task as one of its parameters, the pipeline should treat that upstream task as a dependency. The task with the condition must be scheduled only after the upstream task completes.
- When task results are applied to a pipeline run, the parameter substitution must also extend into the parameters of all conditions associated with each pipeline task. Any result reference placeholders in condition parameters should be replaced with the actual resolved values.

## Why This Matters

Conditions in Tekton pipelines allow users to guard task execution on the outcomes of prior steps. For this to work correctly when a condition's input comes from another task's result, the system must both enforce the correct execution order and supply the actual value at runtime. Without these fixes, conditions depending on task results will either run too early or receive unresolved placeholder strings instead of real values.
