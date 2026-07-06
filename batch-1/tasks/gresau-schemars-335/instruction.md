Implement the ability to generate distinct JSON schemas for Rust types that accurately reflect either serialization or deserialization contexts. Ensure that fields and enum variants are correctly included or excluded based on their serialization attributes, and apply appropriate rename rules for each context.

*   Implement the `for_deserialize()` method in the `SchemaSettings` type:
    *   Exclude fields and enum variants annotated with `skip_deserializing` from the schema.
    *   Include fields and variants annotated with `skip_serializing` with the `writeOnly: true` JSON Schema keyword.
    *   Use deserialize-specific rename rules for field and variant names.

*   Implement the `for_serialize()` method in the `SchemaSettings` type:
    *   Exclude fields and enum variants annotated with `skip_serializing` from the schema.
    *   Include fields and variants annotated with `skip_deserializing` with the `readOnly: true` JSON Schema keyword.
    *   Use serialize-specific rename rules for field and variant names.

*   Ensure correct handling of tuple structs:
    *   For `for_deserialize()`, remove fields annotated with `skip_deserializing` and compact remaining fields. Mark `skip_serializing` fields with `writeOnly: true`.
    *   For `for_serialize()`, remove fields annotated with `skip_serializing` and compact remaining fields. Mark `skip_deserializing` fields with `readOnly: true`.

*   Handle named struct schemas:
    *   Do not list fields annotated with `skip_serializing_if` in the 'required' array for either schema mode.

*   Generate schemas for enums with all tagging styles:
    *   For `for_deserialize()`, exclude `skip_deserializing` variants and include `skip_serializing` variants with appropriate markers.
    *   For `for_serialize()`, exclude `skip_serializing` variants and include `skip_deserializing` variants with appropriate markers.
    *   Apply rename rules based on the context.

*   Use the 'const' keyword for single-value tag constraints in adjacently-tagged enum schemas.

*   Produce a schema of `{"not": {}}` for enums with no valid variants.

*   For decimal number types, specify the type as `["string", "number"]` and update the validation pattern to include scientific notation.

*   In default schema generation mode:
    *   Exclude fields annotated with `skip_deserializing` entirely.
    *   Allow `skip_serializing` fields to appear as `writeOnly`.
    *   Use the `['string', 'number']` type with the scientific notation pattern for decimal types.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.