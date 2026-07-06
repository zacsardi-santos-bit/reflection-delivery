I'm working on a codebase that uses Flow types, and I've run into a parser bug.

*   The Flow parser must successfully parse a bare renders type annotation (the 'renders' keyword followed immediately by a type reference) in all type positions without error: type alias declarations, function parameter type annotations, object property type annotations, arrow function return type annotations, generic type arguments, and component declaration return types.

*   When parsing a bare renders type annotation, the 'renders' keyword must be consumed and discarded; the resulting AST node for the annotated type must be a TsTypeReference pointing to the underlying type (e.g., React.Node), with no special wrapper node for the 'renders' keyword.

*   When parsing a nullable bare renders type annotation (the 'renders' keyword followed by '?' and then a type reference), the resulting AST node must be a TsUnionType containing three types: a TsTypeReference for the underlying type, a TsKeywordType of kind 'null', and a TsKeywordType of kind 'undefined'.

*   The bare renders type syntax must be supported when the parser is configured with the 'components' option enabled, including in component declaration return type positions.

*   The parser must correctly parse bare renders types used as generic type arguments (e.g., inside Array<...>), treating only the underlying type reference as the type argument.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.