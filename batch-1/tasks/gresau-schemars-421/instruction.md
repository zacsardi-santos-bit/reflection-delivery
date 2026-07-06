Implement a configuration option in the schema generation library for Rust to control the inclusion of variant titles in JSON schemas for untagged enums. Ensure that by default, these titles are omitted, but provide an opt-in setting for developers who want them included.

*   Update the `SchemaSettings` struct in `schemars/src/generate.rs`:
    *   Add a new public boolean field named `untagged_enum_variant_titles`.
    *   Initialize `untagged_enum_variant_titles` to `false` by default in all constructors, including `Default::default()` and named constructors such as those for draft07, draft2019-09, and openapi3.

*   Modify the schema generation logic:
    *   When `untagged_enum_variant_titles` is `false`, ensure the generated JSON schema for untagged enums does not include a `"title"` field in the variant subschemas within the `anyOf` array.
    *   When `untagged_enum_variant_titles` is `true`, include a `"title"` field in each variant subschema within the `anyOf` array. The value should be the Rust variant's name as a string (e.g., `"UnitOne"`, `"StringMap"`, `"Tuple"`, `"Struct"`).

*   Ensure consistent behavior across all contexts:
    *   Apply the title-omission default consistently for fully untagged enums and untagged variants within otherwise-tagged enums (adjacently tagged, externally tagged, internally tagged).
    *   Include this behavior for deny-unknown-fields variants, enums with extended schemas, and remote-derived types.

*   Ensure the schema generation settings are accessible at code-generation time:
    *   Allow the derive macro to read `untagged_enum_variant_titles` from `generator.settings()` and apply the conditional title insertion accordingly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.