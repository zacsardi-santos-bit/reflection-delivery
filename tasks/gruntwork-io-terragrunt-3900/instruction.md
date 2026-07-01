Implement support for nested stacks in Terragrunt's stack configuration system by allowing stack configuration files to reference entire other stacks as components. Ensure that stack references are validated for uniqueness and non-emptiness, similar to existing unit validations.

*   Define a `Stack` struct in `config/stack.go` with the following fields:
    *   `Name` (string): The unique label identifying the stack.
    *   `Source` (string): The source location for the stack configuration.
    *   `Path` (string): The relative output path where the stack will be generated.

*   Update the `StackConfigFile` struct in `config/stack.go` to include:
    *   `Stacks` (slice of `*Stack`): A field added alongside the existing `Units` field.

*   Implement the `ValidateStackConfig` function with the signature `ValidateStackConfig(config *StackConfigFile) error`, located in `config/stack_validation.go` or `config/stack.go`, to perform the following validations:
    *   Return no error if all stacks have non-empty, unique names, sources, and paths.
    *   Return an error with the message "stack at index {i} has empty name" if a stack's name is empty or whitespace-only.
    *   Return an error with the message "stack '{name}' has empty source" if a stack's source is empty or whitespace-only.
    *   Return an error with the message "stack '{name}' has empty path" if a stack's path is empty or whitespace-only.
    *   Return an error with the message "duplicate stack name found: '{name}'" if two stacks share the same name.
    *   Return an error with the message "duplicate stack path found: '{path}'" if two stacks share the same path.

*   Ensure `ValidateStackConfig` continues to validate `Unit` entries using existing rules:
    *   Empty or whitespace name returns "unit at index {i} has empty name".
    *   Empty source returns "unit '{name}' has empty source".
    *   Empty path returns "unit '{name}' has empty path".
    *   Duplicate names return "duplicate unit name found: '{name}'".
    *   Duplicate paths return "duplicate unit path found: '{path}'".

*   Ensure a `StackConfigFile` containing only valid `Stacks` entries (no `Units`) passes validation without error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.