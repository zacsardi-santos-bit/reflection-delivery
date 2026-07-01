# Builder Trait: Error Reporting and Pipeline Task Filtering

## Description

Currently, when the builder trait detects an invalid configuration — such as a build strategy that is incompatible with the platform, or an invalid Maven profile reference — it surfaces the problem as a direct error return. This means the error is propagated in the operator's reconciliation loop rather than being recorded on the affected resource's status. Users have no way to inspect the integration kit's status to understand what went wrong, because the failure never reaches the resource conditions.

Additionally, the builder trait offers no way to select a subset of pipeline tasks or reorder them. All build pipeline tasks always run in a fixed sequence regardless of whether a user needs them all, and there is no mechanism to inject custom tasks at a specific position in that sequence. Custom tasks also have no way to specify a container user ID for security context purposes.

## Expected Behavior

- When a validation error occurs during build pipeline configuration, the error should be recorded in the integration kit's status (as a condition with an error phase), rather than being returned as a direct error. This keeps the reconciliation loop intact and gives operators and users visibility into what failed.
- Custom build tasks defined as semicolon-separated strings should support an optional fourth element specifying the container user ID, enabling security context configuration for those tasks.
- Users should be able to specify a task filter — a comma-separated list of task names — that controls which pipeline tasks are included and in what order they execute.
- If the task filter references a task name that does not exist, an appropriate error should be returned.
- If the task filter results in a pipeline where the last task is not a publishing or user-defined task, an appropriate error should be returned.

## Why This Matters

Proper error reporting in resource status conditions is the standard Kubernetes-native way to communicate problems, and gives platform users a consistent place to look for build failure details. Pipeline task filtering enables teams to customize their build workflows — skipping unnecessary steps, reordering stages, or injecting custom steps at precise positions in the build sequence.
