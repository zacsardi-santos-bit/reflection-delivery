I'm working on component definitions that include custom health checks and status display templates.

*   EncodeMetadata must accept unary expression values (e.g. logical negation of a selector) as valid field values in status sub-fields without returning a validation error

*   A package-private function hasDisableValidation must exist in pkg/definition/ast/transformer.go; it accepts a CUE AST field and returns true if the field carries a @disableValidation() attribute, false otherwise; it must correctly handle fields that have multiple attributes

*   When EncodeMetadata processes a status sub-field (details, healthPolicy, or customStatus) whose field has the @disableValidation() attribute: it must bypass structural validation, convert the struct value to a string literal (*ast.BasicLit), and embed each field attribute as a '// cue-attr:@attrName(args)' comment line in the stringified content

*   EncodeMetadata must be idempotent for fields annotated with @disableValidation(): if the stringified field value already contains a '// cue-attr:@disableValidation()' sentinel line, re-encoding must not add a second copy — the sentinel must appear exactly once

*   DecodeMetadata must handle stringified status sub-fields that contain '// cue-attr:@attrName(args)' sentinel comment lines: it must skip validation for those fields and restore all such embedded attributes as entries in the field's Attrs slice

*   After a full YAML storage round-trip simulation (EncodeMetadata → strip all field attributes → DecodeMetadata), DecodeMetadata must succeed without error because the embedded '// cue-attr:' sentinels in the string value carry the exemption intent across the storage boundary

*   When a field has multiple attributes (e.g. @disableValidation() together with another attribute), all of them must be embedded as separate '// cue-attr:' lines during encoding, and after decoding the field's Attrs slice must have the same length as the original number of attributes

*   EncodeMetadata must handle details sub-fields containing root-level for-comprehensions that generate dynamic string keys from list data (the field value for each dynamic key must be a scalar, not a struct), stringifying them successfully and surviving an EncodeMetadata/DecodeMetadata round-trip

*   EncodeMetadata must handle details sub-fields with call expressions that take list comprehension arguments, stringifying them without error and surviving an encode/decode round-trip

*   EncodeMetadata must handle details sub-fields that embed a locally-defined variable at the root level, preserving the content through an encode/decode round-trip

*   When EncodeMetadata encounters a for-comprehension in a status details field where the generated value type is a struct (not a scalar), it must return an error whose message contains the substring '<dynamic>' to clearly identify the problematic dynamic label


*   Interface details: Type: Function
Name: hasDisableValidation
Location: pkg/definition/ast/transformer.go
Signature: hasDisableValidation(field *ast.Field) bool
Description: Package-private helper that inspects the attributes of a CUE AST field and returns true if any of them is the @disableValidation() attribute, false otherwise. Must correctly handle fields with no attributes and fields with multiple attributes.

Type: Function
Name: EncodeMetadata
Location: pkg/definition/ast/transformer.go
Signature: EncodeMetadata(field *ast.Field) error
Description: Encodes a component definition's metadata field (rooted at the "attributes" field of a CUE file). Key behaviors added or changed:
- Unary expression values (e.g. logical negation) must be accepted as valid field values in status sub-fields.
- When a status sub-field (details, healthPolicy, or customStatus) has the @disableValidation() attribute: skip structural validation, convert the struct value to a *ast.BasicLit string literal, and prepend each field attribute as a comment line of the form "// cue-attr:@attrName(args)" inside the stringified content.
- Idempotent: if the target field is already a *ast.BasicLit already containing a "// cue-attr:@disableValidation()" sentinel, do not inject it a second time (the sentinel must appear exactly once).
- Support root-level for-comprehensions with dynamic string keys (value must be a scalar), call expressions with comprehension arguments, and local variable embedding at the root of a details struct.
- For comprehensions where the generated value is a struct (not a scalar), return an error whose message contains the substring "<dynamic>".

Type: Function
Name: DecodeMetadata
Location: pkg/definition/ast/transformer.go
Signature: DecodeMetadata(field *ast.Field) error
Description: Decodes a component definition's metadata field. Key behaviors added or changed:
- When a status sub-field is a *ast.BasicLit string that contains one or more "// cue-attr:@attrName(args)" sentinel comment lines, skip structural validation for that field and restore all such attributes onto the field's Attrs slice (f.Attrs).
- After a YAML round-trip where field attributes have been stripped (f.Attrs = nil), DecodeMetadata must still succeed and restore the attributes from the embedded sentinels.
- The number of restored attributes must match the number of "// cue-attr:" lines present in the string.

Type: Function
Name: GetFieldByPath
Location: pkg/definition/ast/transformer.go
Signature: GetFieldByPath(field *ast.Field, path string) (*ast.Field, bool)
Description: Existing exported helper that traverses a nested CUE AST field by dot-separated path (e.g. "attributes.status.details") and returns the found field and true, or nil and false if not found. Used by tests to inspect encoded output.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.