Implement a semantic analysis library for GraphQL documents to support linting and analysis tools. Ensure the library can identify all name bindings, resolve references, and handle unresolved references, particularly focusing on variable resolution across fragments and operations.

*   Implement the `semantic_model` function in `crates/biome_graphql_semantic/src/semantic_model/mod.rs`:
    *   Accept a `GraphqlRoot` and return a `SemanticModel`.
    *   Traverse the syntax tree in pre-order to extract all bindings, references, and unresolved references.

*   Define the `SemanticModel` struct in `crates/biome_graphql_semantic/src/semantic_model/model.rs`:
    *   Implement `all_bindings(&self) -> impl Iterator<Item = Binding>` to return all name declarations.
    *   Implement `all_unresolved_references(&self) -> impl Iterator<Item = UnresolvedReference>` for unresolved name references.
    *   Implement `all_unresolved_variable_references(&self) -> impl Iterator<Item = UnresolvedVariableReference>` for unresolved variable references.

*   Ensure built-in GraphQL scalar types and directives are not flagged as unresolved:
    *   Scalars: `String`, `Int`, `Float`, `Boolean`, `ID`.
    *   Directives: `skip`, `include`, `deprecated`, `specifiedBy`.

*   Implement the `Binding` struct in `crates/biome_graphql_semantic/src/semantic_model/binding.rs`:
    *   Provide a `syntax(&self) -> &GraphqlSyntaxNode` method.

*   Implement the `UnresolvedReference` struct in `crates/biome_graphql_semantic/src/semantic_model/reference.rs`:
    *   Provide a `syntax(&self) -> &GraphqlSyntaxNode` method.

*   Implement the `UnresolvedVariableReference` struct in `crates/biome_graphql_semantic/src/semantic_model/reference.rs`:
    *   Provide a `syntax(&self) -> &GraphqlSyntaxNode` method.
    *   Implement `referenced_operation(&self) -> Option<GraphqlOperationDefinition>`.

*   Implement the `HasDeclarationAstNode` trait in `crates/biome_graphql_semantic/src/semantic_model/reference.rs`:
    *   Provide `binding_node(&self, model: &SemanticModel) -> Option<Self::DeclarationAstNode>` for resolving references to declarations.

*   Implement the `IsBindingAstNode` trait in `crates/biome_graphql_semantic/src/semantic_model/binding.rs`:
    *   Provide `all_reference_nodes(&self, model: &SemanticModel) -> Vec<Self::ReferenceAstNode>` for retrieving all references to a declaration.

*   Implement the `HasDeclarationAstNodes` trait in `crates/biome_graphql_semantic/src/semantic_model/reference.rs`:
    *   Provide `binding_nodes(&self, model: &SemanticModel) -> Vec<Self::DeclarationAstNode>` for variable references bound in multiple operations.

*   Ensure type extension nodes bind to their base type definitions, not creating new bindings.

*   Flag directives not defined in the document and not built-in as unresolved references.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.