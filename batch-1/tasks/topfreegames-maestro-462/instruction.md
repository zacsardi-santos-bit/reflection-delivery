Fix the bugs in the game room and scheduler management code to ensure proper validation and patching functionality. Implement changes to make port configuration optional and allow container name updates through patch operations.

*   Update the `Container` struct in `internal/core/entities/game_room/container.go`:
    *   Ensure the `Ports` field does not have the 'required' validation tag, allowing containers without ports to pass validation.

*   Modify the `Spec` struct in `internal/core/entities/game_room/spec.go`:
    *   Add a 'semantic_version' validation tag to the `Version` field. Ensure it produces an error with the message "Error:Field validation for 'Version' failed on the 'semantic_version' tag" if a non-semantic-version string is provided.
    *   Add a 'gt' validation tag to the `TerminationGracePeriod` field. Ensure it produces an error with the message "Error:Field validation for 'TerminationGracePeriod' failed on the 'gt' tag" if the value is less than or equal to zero.

*   Implement the `NewSpec` function in `internal/core/entities/game_room/spec.go`:
    *   Accept parameters: `version` (string), `terminationGracePeriod` (time.Duration), `containers` ([]Container), `toleration` (string), `affinity` (string).
    *   Return a pointer to a `Spec` struct with fields set to the provided arguments.

*   Implement the `RegisterValidations` function in `internal/validations/validations.go`:
    *   Register custom validators, including the 'semantic_version' rule.
    *   Ensure it returns nil on success and is safe to call before using `Validate.Struct`.

*   Ensure the `Validate` variable in `internal/validations/validations.go`:
    *   Is an exported validator instance capable of validating structs according to their field tags.

*   Update the `patchContainers` function or the exported `PatchScheduler` function in `internal/core/services/scheduler_manager/patch_scheduler/patch_scheduler.go`:
    *   Handle the container name key in the patch map, ensuring `containers[i].Name` is set to the patched value.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.