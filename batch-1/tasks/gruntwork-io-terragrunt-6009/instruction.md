Implement logic in the `ParseConfig` function within `pkg/config/config.go` to ensure that exclusion blocks defined in parent configurations are correctly inherited by child configurations. Follow these requirements to fix the issue:

*   Ensure that when a child configuration includes a parent configuration with an `exclude` block (containing `if`, `actions`, and `no_run` fields), and the child does not define its own `exclude` block, the parsed child configuration inherits the parent's `exclude` block with all field values intact.
    *   The `Exclude` field in the parsed result must be non-nil and reflect the parent's settings.
*   Ensure that `exclude` block inheritance functions correctly for all merge strategies:
    *   Default merge (no `merge_strategy` specified).
    *   Shallow merge (`merge_strategy = "shallow"`).
    *   Deep merge (`merge_strategy = "deep"`).
*   When both parent and child configurations define an `exclude` block, ensure the child's `exclude` block takes precedence in the final merged configuration, overriding the parent's values, for all merge strategies.
*   Ensure that the `errors` block defined in a parent configuration (including `retry` configuration with `label`, `max_attempts`, and `sleep_interval_sec` fields) is inherited by child configurations that include that parent and do not define their own `errors` block.
*   Ensure that the `engine` block defined in a parent configuration (with `source`, `version`, and `type` fields) is inherited by child configurations that include that parent and do not define their own `engine` block.
*   Ensure that `feature flag` blocks defined in a parent configuration (with `name` and `default` fields) are inherited by child configurations that include that parent and do not define their own feature flags.
*   Update the `ParseConfig` function to conditionally assign the `Exclude` field in the merged configuration:
    *   Replace the parent's inherited `Exclude` only when the child configuration explicitly defines its own `exclude` block (i.e., only when the child's `Exclude` is not nil).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.