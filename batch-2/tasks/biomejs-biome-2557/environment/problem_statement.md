## Description

The Biome GraphQL parser does not currently support parsing schema definitions, which are a core construct in GraphQL Schema Definition Language. A schema definition declares which types serve as the entry points for query, mutation, and subscription operations. Without this support, any GraphQL document containing a schema block fails to parse correctly, making it impossible to analyze or lint real-world schema files.

## Expected Behavior

- A schema block with any combination of query, mutation, and subscription root operation type entries should parse successfully into a properly structured AST node.
- A schema definition optionally preceded by a string or block string description should also parse correctly, with the description captured as part of the definition.
- When a schema block contains invalid operation type keywords (not one of the three recognized operation types), the parser should produce a clear error message identifying the unexpected token and what was expected instead.
- When a schema block is missing its closing brace, or when a root operation type entry is missing its type reference, the parser should produce a targeted diagnostic pointing to exactly what was found and what was expected.
- Unterminated description strings before a schema definition should be flagged with an appropriate error.
- Error recovery should allow subsequent definitions in the file to continue parsing after a malformed schema definition.

## Why This Matters

GraphQL schema files are the foundation of any API built with GraphQL. Without schema definition support, tools built on Biome cannot process or validate schema documents at all, severely limiting their usefulness for backend and full-stack GraphQL development.
