Implement the `FieldMaskFromRequestBody` function to ensure that fields with empty object values in a JSON request body are included in the generated field mask. This will ensure that all client-specified fields, even those set to empty objects, are recognized and processed.

*   Update the `FieldMaskFromRequestBody` function located in `runtime/fieldmask.go` to handle fields with empty object values.
    *   Ensure that when a field in the JSON body is set to an empty object (`{}`), its path is included in the resulting `FieldMask`.
    *   Treat fields mapping to message types with no sub-fields (empty objects) as leaf nodes, appending their paths to the `FieldMask` paths.
*   Ensure that the function signature remains:
    *   `FieldMaskFromRequestBody(r io.Reader, msg proto.Message) (*field_mask.FieldMask, error)`
    *   This function should parse a JSON request body and return a `FieldMask` representing all fields present, including those with empty object values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.