Fix the array index out-of-bounds error in group-by queries and implement a configurable trim size for the multi-stage aggregation operator in Pinot. Ensure that group-by queries return trimmed results without crashing and allow users to set and override trim sizes via query options and hints.

*   Update the `AggregateOperator` class:
    *   Implement the `getGroupTrimSize()` method, annotated with `@VisibleForTesting`, to return an `int` representing the effective trim size.
    *   Return `Integer.MAX_VALUE` from `getGroupTrimSize()` when no trim size is configured.
    *   Return the parsed integer value from the execution context if the `GROUP_TRIM_SIZE` query option key (`'groupTrimSize'`) is present.
    *   Return the parsed integer value from the aggregate node hint if the `GROUP_TRIM_SIZE` aggregate hint option (`'group_trim_size'`) is set, taking priority over the execution context.

*   Define constants for group trim size:
    *   In `CommonConstants.java`, ensure the existence of `public static final String GROUP_TRIM_SIZE = "groupTrimSize"`.
    *   In `PinotHintOptions.java`, ensure the existence of `public static final String GROUP_TRIM_SIZE = "group_trim_size"`.

*   Modify group-by aggregation queries:
    *   Prevent `ArrayIndexOutOfBoundsException` in dictionary-based group key generation when `numGroupsLimit` is smaller than the number of distinct group keys.
    *   Ensure that query results are trimmed to at most `numGroupsLimit` groups without crashing.

*   Update `TableConfigBuilder`:
    *   Implement the `addFieldConfig(FieldConfig config)` method to append the given `FieldConfig` to the builder's field config list, initializing the list if necessary, and return the builder for method chaining.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.