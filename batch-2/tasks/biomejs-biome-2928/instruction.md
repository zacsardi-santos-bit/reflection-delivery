Implement support for object type extension statements in the GraphQL parser of Biome. Ensure the parser correctly handles all valid forms of these extensions and provides clear error messages for invalid forms. Update the AST node structure to accommodate these extensions.

*   Recognize 'extend type <Name>' statements and produce a GraphqlObjectTypeExtension AST node.
    *   Use the syntax kind constant GRAPHQL_OBJECT_TYPE_EXTENSION.
    *   Ensure the node has 6 ordered slots:
        *   Slot 0: extend keyword token (required)
        *   Slot 1: type keyword token (required)
        *   Slot 2: name as GraphqlName (required)
        *   Slot 3: implements as GraphqlImplementsInterfaces (optional)
        *   Slot 4: directives as GraphqlDirectiveList (always present, may be empty)
        *   Slot 5: fields as GraphqlFieldsDefinition (optional)
*   Ensure GraphqlObjectTypeExtension is a direct member of AnyGraphqlDefinition.
*   Parse the following forms into GraphqlObjectTypeExtension without errors:
    *   Extension with a fields block only
    *   Extension with a directive only
    *   Extension with an implements clause only
    *   Extension with implements + directive
    *   Extension with directive + fields
    *   Extension with implements + directive + fields
*   Emit specific error messages for invalid statements:
    *   "Expected at least one directive, implements interface or fields definition" when no implements clause, directives, or fields block are present.
    *   "expected `{` but instead found `<token>`" when a fields block is missing its opening '{' token, with a hint to "Remove <token>".
*   Implement error recovery to continue parsing after encountering an invalid object type extension.
*   Update the parser implementation in `crates/biome_graphql_parser`.
*   Define the node in `crates/biome_graphql_syntax`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.