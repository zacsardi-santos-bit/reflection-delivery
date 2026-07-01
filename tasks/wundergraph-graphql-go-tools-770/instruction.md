Fix the JSON schema generator for GraphQL input types to correctly handle nullable references and distinct definitions for required and optional fields. Ensure the generator uses the correct JSON Schema combinators and produces separate definitions as needed.

*   Implement the `NullableRef` struct in `v2/pkg/graphqljsonschema/jsonschema.go`:
    *   Use the JSON key "anyOf" for its array field.
    *   Signature: `AnyOf []JsonSchema `json:"anyOf"``
*   Implement the `NewNullableRef` function in `v2/pkg/graphqljsonschema/jsonschema.go`:
    *   Create a `NullableRef` that wraps a null schema and a reference to the named definition using the "anyOf" combinator.
    *   Signature: `NewNullableRef(definitionName string) NullableRef`
*   Update the `fromTypeRef` method in `v2/pkg/graphqljsonschema/jsonschema.go`:
    *   Ensure it resolves a GraphQL type reference into a `JsonSchema`.
    *   Use the `field bool` parameter to determine if the type is a field within an object.
    *   Store non-null (required) definitions under the key "{TypeName}NotNull" in the $defs map.
    *   Signature: `(r *fromTypeRefResolver) fromTypeRef(operation, definition *ast.Document, typeRef int, field bool) JsonSchema`
*   Ensure JSON schema generation adheres to the following rules:
    *   Use 'anyOf' for nullable optional fields referencing other input types.
    *   Produce a single definition named 'TypeName' with type ["object","null"] for types used only as optional fields.
    *   Produce a single definition named 'TypeNameNotNull' with type ["object"] for types used only as required fields.
    *   Generate two separate definitions ('TypeName' and 'TypeNameNotNull') when a type is used as both required and optional.
    *   Ensure field declaration order in GraphQL schema does not affect JSON schema output.
    *   Use 'anyOf' for array items of nullable input object types.
    *   Apply rules consistently in recursive and deeply nested schemas.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.