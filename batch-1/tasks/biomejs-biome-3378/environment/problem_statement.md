## Description

The biome project supports GraphQL linting, but there is currently no semantic model for GraphQL documents. Without a semantic model, it is impossible for lint rules and analysis tools to understand the relationships between declarations and references in a GraphQL schema or query document — for example, knowing that a type used in a field's return type is actually defined somewhere in the schema, or that a fragment spread refers to a valid fragment definition.

## Expected Behavior

A new semantic analysis library for GraphQL documents should be introduced. It should be able to:

- Identify all name bindings (declarations) in a document, including type definitions, directive definitions, fragment definitions, operation definitions, and variable definitions.
- Identify all name references that could not be resolved to any declaration, flagging them as unresolved references.
- Understand that standard built-in scalar types and built-in directives are always defined and should never be flagged as unresolved.
- Resolve type extension constructs to their original base type definition rather than treating them as new bindings.
- Resolve variable references inside fragments to the variable definitions in the operations that include those fragments — including transitive fragment inclusion and cases where the same fragment is used by multiple operations.
- Identify variable references in fragments that remain unresolved because the fragment is not used by any operation, or because the including operation does not declare the variable. For unresolved variable references that are reachable via an operation, the system should report which operation they belong to.
- Allow callers to navigate from any reference node to its declaration node, and from any declaration node to all of its reference nodes.

## Why This Matters

Without this semantic model, GraphQL lint rules cannot detect issues like undeclared type references, missing variable declarations, or fragment spreads that reference non-existent fragments. This foundation enables the development of accurate, schema-aware lint rules for GraphQL.
