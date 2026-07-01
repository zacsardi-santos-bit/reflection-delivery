Add a diagnostic to the parser to detect and report errors when a binding-with-else statement uses a struct literal as its initializer. Ensure the parser emits an error message and recovers to produce a valid parse tree.

*   Detect when a binding-with-else statement has a struct literal as its initializer.
    *   Identify the pattern where a closing curly brace appears immediately before the else keyword.
*   Emit an error when this pattern is detected.
    *   Place the error at the byte offset of the closing brace of the struct literal.
    *   Use the error message: "right curly brace `}` before `else` in a `let...else` statement not allowed".
*   Ensure the parser recovers from the error and produces a valid parse tree.
    *   The parse tree must include:
        *   LET_STMT node
        *   IDENT_PAT node
        *   RECORD_EXPR node with RECORD_EXPR_FIELD_LIST
        *   LET_ELSE node with its BLOCK_EXPR
        *   SEMICOLON node

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.