Implement support for parsing schema definitions in the Biome GraphQL parser. Ensure that valid schema blocks are parsed into a structured AST and handle error cases with appropriate diagnostics, allowing the parser to recover and continue processing subsequent definitions.

Requirements:

*   Recognize schema definitions as a top-level definition type:
    *   Produce a `GraphqlSchemaDefinition` CST node with:
        *   An optional `GraphqlDescription` node for descriptions.
        *   A `SCHEMA_KW` token for the schema keyword.
        *   An optional directives list.
        *   A left curly brace token.
        *   A `GraphqlRootOperationTypeDefinitionList` containing root operation type definitions.
        *   A right curly brace token.
*   Parse each entry inside a schema block as a `GraphqlRootOperationTypeDefinition` node:
    *   Include a `GraphqlOperationType` node with one of the keywords: `QUERY_KW`, `MUTATION_KW`, or `SUBSCRIPTION_KW`.
    *   Include a colon token and a named type reference.
*   Handle optional description strings:
    *   Parse and store as a `GraphqlDescription` node containing a `GraphqlStringValue`.
*   Emit diagnostics for invalid operation type keywords:
    *   Message: `Expected a query, a mutation, or a subscription but instead found 'X'.`
    *   Produce a `GraphqlBogus` node for invalid entries.
*   Handle entire schema blocks with invalid entries:
    *   Produce a `GraphqlBogusDefinition` node.
*   Handle missing operation type keywords before a colon:
    *   Produce a `GraphqlRootOperationTypeDefinition` with missing operation_type.
    *   Emit diagnostic: `Expected a query, a mutation, or a subscription here.`
*   Handle missing named types after valid operation types and colons:
    *   Emit diagnostic: `Expected a named type but instead found 'X'.`
    *   Info note: `Expected a named type here.`
*   Detect unterminated description strings:
    *   Emit diagnostic: `Missing closing quote`.
*   Handle unclosed schema blocks:
    *   Emit diagnostic: `expected \`}\` but instead found \`X\``.
    *   Hint: `Remove X`.
    *   Produce a `GraphqlSchemaDefinition` with `r_curly_token` marked as missing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.