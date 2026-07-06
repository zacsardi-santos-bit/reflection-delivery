Implement improvements to the builder trait in Apache Camel-K to enhance error reporting and pipeline task filtering. Ensure validation errors are recorded in the integration kit's status and allow users to specify and reorder pipeline tasks, including custom tasks with security context.

*   Modify the `Apply` method in `pkg/trait/builder.go`:
    *   When encountering a validation error (e.g., incompatible build strategy or invalid Maven profile), return `nil` and set `env.IntegrationKit.Status.Phase` to `v1.IntegrationKitPhaseError`.
    *   Add a condition with `Status: corev1.ConditionFalse` and `Type: v1.IntegrationKitConditionType("IntegrationKitTasksValid")` to the integration kit's status.
    *   Implement task filtering using the `TasksFilter` field:
        *   If `TasksFilter` is empty, include all pipeline tasks in their default order.
        *   If `TasksFilter` is set, filter and reorder `env.Pipeline` to include only specified tasks in the given order.
        *   Return an error "no task exist for <name> name" if a task in `TasksFilter` does not exist.
        *   Return an error "last pipeline task is not a publishing or a user task" if the last task in the filtered list is invalid.

*   Update the `customTasks` method in `pkg/trait/builder.go`:
    *   Change the signature to `customTasks(tasks []v1.Task, kitImage string) ([]v1.Task, error)`.
    *   Ensure task strings have at least 3 semicolon-separated parts; otherwise, return an error: "provide a custom task with at least 3 arguments, ie \"my-task-name;my-image;echo 'hello'\", was <task-string>".
    *   If a task string includes a fourth numeric user ID, set the `Custom.ContainerUserID` field as a `*int64` pointer with the parsed value.

*   Add the `TasksFilter` field to the `builderTrait` struct in `pkg/trait/builder.go`:
    *   Define it as a string that specifies a comma-separated list of task names to control task inclusion and order.

*   Modify `NewFakeClient` in `pkg/util/test/client.go`:
    *   Register `IntegrationKit` as a status subresource by calling `WithStatusSubresource(&v1.IntegrationKit{})` on the fake client builder.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.