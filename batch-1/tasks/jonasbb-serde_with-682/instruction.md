Implement a solution to ensure that the serialization transformation macro respects existing JSON schema type annotations on struct fields, preventing compilation errors due to conflicting annotations. Detect and handle both unconditional and conditional schema annotations appropriately.

*   Update the `serde_as` attribute macro to:
    *   Detect when a struct field already has an explicit `schemars` schema-type annotation (e.g., `schemars(with = ...)`) and avoid injecting an automatic schema annotation for that field.
    *   Ensure that when a field has an unconditional `schemars` schema-type annotation alongside a `serde_as` transformation, the generated JSON schema uses the user-specified type.
    *   Handle conditionally-disabled schema annotations (e.g., using `cfg_attr` with a condition that is always false) by treating them as absent and allowing the macro to inject its own automatic schema.
    *   Handle conditionally-enabled schema annotations (e.g., using `cfg_attr` with a condition that is always true) by treating them as present and avoiding the injection of an automatic schema, using the user-specified type instead.

*   Ensure that all existing tests for the `schemars` schema generation integration continue to compile and pass after implementing these changes.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.