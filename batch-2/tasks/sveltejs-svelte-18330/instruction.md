Implement changes to the Svelte template compiler to correctly handle the use of "type" as a regular variable identifier in expressions and improve parsing in recovery mode. Ensure the compiler distinguishes between valid JavaScript expressions and TypeScript type alias declarations.

*   Update the parser to handle incomplete variable declarations in loose/recovery mode:
    *   If a declaration tag ends with a division operator and no right operand (e.g., `{let x = a / }`), produce a DeclarationTag AST node.
    *   Ensure the node's declaration is a VariableDeclaration with kind 'let', containing one declarator with an empty identifier name ('') and a null initializer.

*   Modify the parser to correctly parse template expressions involving "type" as an identifier:
    *   For expressions like `{type instanceof /* comment */ Object}`, parse as an ExpressionTag.
    *   Ensure the AST expression is a BinaryExpression with operator 'instanceof', left Identifier 'type', and right Identifier 'Object'.
    *   Include the block comment as a leading comment on the right-side identifier node and in the root node's top-level comments array.

*   Adjust the validator to handle expressions using "type" as a regular identifier:
    *   Do not emit a declaration_tag_invalid_type error for binary expressions where 'type' is on the left side, including operators like ===, !==, ==, !=, +, -, *, /, >, instanceof, and in.

*   Maintain error handling for TypeScript type alias declarations:
    *   Continue emitting a declaration_tag_invalid_type error with code 'declaration_tag_invalid_type' and message "Declaration tags must be `let` or `const` declarations" for actual TypeScript type alias declarations inside declaration tags, including in TypeScript script blocks.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.