Implement a more expressive, rule-based validation system for Kubernetes API types in Go. Enhance the existing comment-driven schema generation system to support indexed validation rule entries directly in Go comments, allowing for complex validation expressions and error handling.

*   Update the `CELTag` struct:
    *   Include fields: `Rule` (string), `Message` (string), `MessageExpression` (string), `OptionalOldSelf` (*bool).
    *   Ensure all fields are optional/omitempty.
*   Modify the `CommentTags` struct:
    *   Add a `CEL` field of type `[]CELTag`.
    *   Retain or rename the `spec.SchemaProps` field as needed.
*   Enhance `ParseCommentTags` function:
    *   Parse indexed CEL entries using the syntax `cel[N]:field=value`.
    *   Populate `CommentTags.CEL` slice in index order.
    *   Treat a boolean CEL field without a value as true (`*bool` pointer to true).
    *   Enforce strict consecutive indices for CEL entries.
    *   Return error for non-consecutive indices: `'failed to parse marker comments: error parsing <full_original_comment_line>: non-consecutive index <N> for key '+k8s:validation:cel''`.
    *   Reset consecutive-index tracking on encountering non-prefixed marker comments.
    *   Return error for duplicate non-CEL keys: `'failed to parse marker comments: cannot have multiple values for key '<key>'`.
    *   Return error for invalid JSON values: `'failed to unmarshal marker comments: json: cannot unmarshal string into Go struct field CommentTags.<field> of type <type>'`.
    *   Directly return type-constraint and value-constraint validation errors prefixed with `'invalid marker comments: '`.
*   Ensure OpenAPI schema generator includes `x-kubernetes-validations` extension:
    *   Populate `VendorExtensible.Extensions` with CEL rules.
    *   Use `ptr.To[bool](true)` for `optionalOldSelf=true` in generated code, adding `k8s.io/utils/ptr` to imports.
*   Update integration test golden files:
    *   Include `x-kubernetes-validations` entries for types and fields with CEL markers.
    *   Ensure `openapi_generated.go` reflects these extensions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.