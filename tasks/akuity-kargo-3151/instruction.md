Implement a JSON file update feature as part of a promotion step in Kargo. Create a new step that takes a JSON file path and a list of key-value updates, supporting dot-notation for nested fields. Ensure the feature handles strings, numbers, and booleans, validates configurations, and generates commit messages.

*   Implement `newJSONUpdater()` in `internal/directives/json_updater.go`:
    *   Return a `PromotionStepRunner` that is type-assertable to `*jsonUpdater`.

*   Define `jsonUpdater` struct in `internal/directives/json_updater.go`:
    *   Implement `validate(cfg Config) error`:
        *   Validate configuration using a JSON schema.
        *   Ensure 'path' is required and has `minLength` 1.
        *   Ensure 'updates' is required and has at least 1 item.
        *   Ensure each update's 'key' is required and has `minLength` 1.
        *   Ensure each update's 'value' is required.
    *   Implement `updateFile(workDir string, path string, updates []JSONUpdate) error`:
        *   Read and update the JSON file at `path.Join(workDir, path)`.
        *   Apply updates using dot-notation keys.
        *   Handle string, numeric, and boolean values.
        *   Create new nested keys as needed.
        *   Treat empty files as empty JSON objects.
        *   Return an error containing 'no such file or directory' if the file does not exist.
    *   Implement `generateCommitMessage(path string, updates []JSONUpdate) string`:
        *   Return a formatted commit message for non-empty updates.
        *   Format string values with quotes; non-string values without quotes.
    *   Implement `runPromotionStep(ctx context.Context, stepCtx *PromotionStepContext, cfg JSONUpdateConfig) (PromotionStepResult, error)`:
        *   Return success with a commit message for non-empty updates.
        *   Return success without output for empty updates.
        *   Return an error with 'JSON file update failed' on update failure.

*   Define `JSONUpdateConfig` struct in `internal/directives/zz_config_types.go`:
    *   Fields: `Path string` (json tag 'path'), `Updates []JSONUpdate` (json tag 'updates').

*   Define `JSONUpdate` struct in `internal/directives/zz_config_types.go`:
    *   Fields: `Key string` (json tag 'key'), `Value interface{}` (json tag 'value').

*   Create `internal/directives/schemas/json-update-config.json`:
    *   Define schema requiring 'path' (string, minLength:1) and 'updates' (array, minItems:1).
    *   Ensure each update requires 'key' (string, minLength:1) and 'value'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.