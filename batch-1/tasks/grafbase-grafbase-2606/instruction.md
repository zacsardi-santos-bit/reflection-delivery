Implement a validation mechanism for directive argument values in a federated GraphQL gateway to ensure type correctness at schema construction time. Validate against declared types in the extension SDL and provide detailed error messages for any discrepancies.

*   Validate all directive argument values at schema build time against declared types in the extension SDL.
    *   Apply validation to both field-level and schema-level directives.
*   Generate error messages with specific formats:
    *   Field-level directive errors: "At {TypeName}.{fieldName} for the extension '{extension_url}' directive named '{directive_name}': {error_detail}".
    *   Schema-level directive errors: "At subgraph named '{subgraph_name}' for the extension '{extension_url}' directive named '{directive_name}': {error_detail}".
*   Handle type reference errors:
    *   Undefined type: "Unknown type '{TypeName}'".
    *   Non-input type: "Type '{TypeName}' is used for an input value but is not a scalar, input object or enum."
*   Validate enum values:
    *   Accept valid enum values as their string name.
    *   Unknown enum value: "Found an unknown enum value '{VALUE}' for the enum {EnumType} at path '{path}'".
    *   Non-enum literal: "Found a {ActualType} value where we expected a {EnumType} enum value at path '{path}'".
*   Validate input object fields:
    *   Allow missing nullable fields; omit from output.
    *   Missing required fields: "Found a null where we expected a {Type}! at path '{path}'".
    *   Unknown field: "Input object {InputType} does not have a field named '{fieldName}' at path '{path}'".
    *   Non-object value: "Found a {ActualType} value where we expected a '{InputType}' input object at path '{path}'".
*   Handle argument name errors:
    *   Unknown argument: "Unknown argumant named '{argName}'".
    *   Missing required argument: "Missing required argument named '{argName}'".
    *   Allow omitting nullable arguments.
*   Distinguish null values:
    *   Non-null argument with null: "Found a null where we expected a {Type}! at path '{path}'".
    *   Nullable argument with null: Key present with null in output.
    *   Omitted argument: Key absent from output.
*   Apply default values:
    *   Use default values when arguments are omitted.
    *   Apply field-level defaults for missing fields in partial input objects.
*   Coerce single values to lists:
    *   Single value to single-element list.
    *   Single value to doubly-wrapped list for list-of-list expectations.
    *   Wrong nesting level: "Found a {ActualType} value where we expected a {ExpectedType} at path '{path}.{index}'".
*   Handle integer and float coercions:
    *   Integer to Float with decimal suffix.
    *   Whole-number floats to Int and BigInt.
    *   Non-integer floats for Int: "Found a Float value where we expected a Int scalar at path '{path}'".
    *   Non-integer floats for BigInt: "Found a Float value where we expected a BigInt scalar at path '{path}'".
    *   Out-of-range integers: "Found value {value} which cannot be coerced into a Int scalar at path '{path}'".
*   Validate standard scalar type mismatches:
    *   Produce error: "Found a {ActualType} value where we expected a {ScalarName} scalar at path '{path}'".
    *   Allow any value for custom scalars without validation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.