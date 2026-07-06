Implement improvements to the Python parser to handle f-strings with syntax errors more accurately. Ensure that each malformed f-string is treated as an independent expression and that error messages are precise and contextually appropriate.

*   Modify the parser to handle consecutive f-strings with unclosed expression braces:
    *   Each f-string should produce a separate `StmtExpr / ExprFString` AST node.
    *   Avoid combining them into a single expression with `FStringValue { inner: Concatenated([...]) }`.
    *   Ensure byte ranges are specific to each f-string token.

*   Update error handling for triple-quoted f-strings with unclosed braces:
    *   Produce an `ExprSet` node.
    *   Emit errors: 'missing closing quote in string literal' and 'Expected FStringEnd, found FStringMiddle'.

*   Improve error messages for f-strings with unclosed expression braces:
    *   Emit 'f-string: unterminated string' at the end of the unclosed expression.
    *   Emit 'f-string: expecting \'}\'  ' at the start of the token found instead of '}'.

*   Handle f-strings with unclosed braces in format specifiers:
    *   Emit 'f-string: expecting \'}\'' error message.

*   Extend the test resource file `re_lex_logical_token.py` with new test cases:
    *   Add a comment: '# F-strings uses normal list parsing, so test those as well'.
    *   Include two if-statement test cases:
        *   A function call argument with an f-string having an unclosed expression brace, followed by an indented `def` block.
        *   A function call argument with an f-string terminated by a newline, followed by an indented `def` block.

*   Enhance parser recovery for f-strings in function call arguments within if-conditions:
    *   Emit specific errors for unterminated strings and unclosed braces.
    *   Parse indented function definitions correctly as part of the if-statement or as top-level functions, depending on the context.

*   Prevent spurious errors during unterminated f-string newline recovery:
    *   Stop adding extra errors once the parser context is re-established.

*   Update list-parsing recovery logic in `parser/mod.rs`:
    *   Re-lex tokens as logical line tokens when recognized as part of an enclosing list context.
    *   Emit errors for unrecognized tokens only after confirming they are not list elements or terminators.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.