Implement a GraphQL schema diffing library that compares two schema versions and reports differences in a structured format. The library should take two schema strings as input and return a sorted list of changes, each with a kind and path.

Requirements:

*   Implement the `diff` function in `engine/crates/graphql-schema-diff/src/lib.rs`:
    *   Accept two GraphQL schema SDL strings (source and target).
    *   Return a `Result<Vec<Change>, async_graphql_parser::Error>` with detected differences or an error if parsing fails.
    *   Ensure the returned list of `Change` objects is sorted by path (lexicographic order) and `ChangeKind` variant order.

*   Define the `Change` struct in `engine/crates/graphql-schema-diff/src/change.rs`:
    *   Serialize to JSON with fields: 'kind' (string matching `ChangeKind` variant) and 'path' (dot-separated string).
    *   Use an empty string for schema-level changes in the 'path' field.

*   Define the `ChangeKind` enum in `engine/crates/graphql-schema-diff/src/change.rs`:
    *   Include all specified variants: AddField, RemoveField, AddObjectType, RemoveObjectType, etc.
    *   Ensure each variant serializes to JSON as a string matching its name exactly.

*   Detect and report changes with appropriate `ChangeKind` and path:
    *   Object types: Add/Remove with kind `AddObjectType`/`RemoveObjectType` and path 'TypeName'.
    *   Fields: Add/Remove with kind `AddField`/`RemoveField` and path 'TypeName.fieldName'.
    *   Directive definitions: Add/Remove with kind `AddDirectiveDefinition`/`RemoveDirectiveDefinition` and path equal to the directive name.
    *   Enum types and values: Add/Remove with kind `AddEnum`/`RemoveEnum` and path 'EnumName'; values with kind `AddEnumValue`/`RemoveEnumValue` and path 'EnumName.VALUE_NAME'.
    *   Field arguments: Add/Remove with kind `AddFieldArgument`/`RemoveFieldArgument` and path 'TypeName.fieldName.argName'.
    *   Default values: Add/Remove/Change with kind `AddFieldArgumentDefault`/`RemoveFieldArgumentDefault`/`ChangeFieldArgumentDefault` and path 'TypeName.fieldName.argName'.
    *   Field and argument type changes: Report with kind `ChangeFieldType`/`ChangeFieldArgumentType`.
    *   Interface implementations: Add/Remove with kind `AddInterfaceImplementation`/`RemoveInterfaceImplementation` and path 'TypeName.InterfaceName'.
    *   Root type changes: Report with kind `ChangeQueryType`, `ChangeMutationType`, or `ChangeSubscriptionType` with an empty path.
    *   Schema definition block: Add/Remove with kind `AddSchemaDefinition`/`RemoveSchemaDefinition` with an empty path.
    *   Union types and members: Add/Remove with kind `AddUnion`/`RemoveUnion` and path 'UnionName'; members with kind `AddUnionMember`/`RemoveUnionMember` and path 'UnionName.MemberTypeName'.

*   Ensure the diff function reports both removal of the old kind and addition of the new kind when a type changes its fundamental kind.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.