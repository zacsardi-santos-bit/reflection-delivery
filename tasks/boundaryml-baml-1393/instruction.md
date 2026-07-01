Fix the streaming partial deserializer to correctly handle union types in array fields by using each item's actual concrete class to resolve field requirements. Ensure that all items in arrays are processed accurately according to their specific class schema, preserving the input structure and filling absent fields with nulls appropriately.

*   Modify the `process_node` function in `engine/baml-lib/jsonish/src/deserializer/semantic_streaming.rs`:
    *   Ensure it handles `BamlValueWithMeta::Class(class_name, ...)` by passing the resolved `class_name` string directly to `needed_fields` and `fields_needing_null_filler`.
*   Update the `needed_fields` and `fields_needing_null_filler` functions:
    *   Accept a `class_name: &str` parameter.
    *   Use the `class_name` to directly look up the class in the schema registry.
    *   Do not attempt to extract a class name from a `FieldType`, as it may be a union or non-class type.
*   Ensure that when processing arrays typed as a union of class types:
    *   All items are deserialized according to their concrete class schema.
    *   Present fields are preserved, and absent fields are filled with nulls based on the item's class schema.
*   Preserve arrays containing mixed primitive types (e.g., strings, integers, floats) nested inside union-typed class instances.
*   Verify that the deserialized output matches the input for complete, valid payloads:
    *   All items in both primary and secondary array fields must be present and ordered correctly, with all fields intact.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.