Add support for expressing OpenAPI value-validation constraints directly in Go source code comments. Implement a system to parse these constraints from specially-formatted annotations and inject them into the generated OpenAPI schema. Ensure the constraints are validated for correctness and compatibility with the associated Go field types.

*   Implement the `ParseCommentTags` function in the `pkg/generators/` package:
    *   Parse marker comments from a slice of strings, recognizing lines that begin with '+' followed by the specified prefix.
    *   Parse each matching line as a key=value pair or as a key-only for boolean fields (defaulting to true).
    *   Use JSON marshal/unmarshal to convert parsed marker values into a `CommentTags` struct.
    *   Skip values in the form 'ref(...)' without error.
    *   Strip double quotes from string values in the source comment.
    *   Return an error for invalid values, duplicate keys, or empty keys.

*   Define the `CommentTags` struct in the `pkg/generators/` package:
    *   Export the struct and embed `spec.SchemaProps` to access fields like Minimum, Maximum, MinLength, etc.
    *   Implement `Validate()` method to check internal consistency of constraint values, returning specific error messages for inconsistencies.
    *   Implement `ValidateType(t *types.Type)` method to ensure constraints are appropriate for the given Go type, returning specific error messages for type mismatches or negative values.

*   Update the OpenAPI code generator:
    *   Read '+k8s:validation:*' marker comments and inject constraints into the generated schema.
    *   Use the import alias 'ptr "k8s.io/utils/ptr"' for pointer-type schema fields.
    *   Emit validation properties in a fixed order: Minimum, Maximum, ExclusiveMinimum, ExclusiveMaximum, MinLength, MaxLength, MinProperties, MaxProperties, Pattern, MultipleOf, MinItems, MaxItems, UniqueItems.
    *   Position validation properties correctly within generated schema code based on the type's definition method.
    *   Ensure types with a full custom `OpenAPIDefinition()` method are not affected by marker comment constraints.
    *   Inject constraints into the V2 fallback schema for types with a partial `OpenAPIV3Definition()`.
    *   Inject constraints into both V3 and V2 fallback schemas for types with `OpenAPIV3OneOfTypes()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.