Update the error reporting for pipeline steps to use unique identifiers instead of step names. Implement changes to ensure error messages include these identifiers, improving traceability and clarity in logs and debugging tools.

*   Modify the `ExitError` struct in `pipeline/error.go`:
    *   Replace the `Name` field with a `UUID` field of type `string`.
    *   Ensure the struct includes an `int` field named `Code`.
    *   Implement the `Error()` method to return a string formatted as `'uuid=<UUID>: exit code <Code>'`.
*   Modify the `OomError` struct in `pipeline/error.go`:
    *   Replace the `Name` field with a `UUID` field of type `string`.
    *   Implement the `Error()` method to return a string formatted as `'uuid=<UUID>: received oom kill'`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.