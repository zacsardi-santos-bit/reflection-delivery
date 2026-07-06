I'm working on the F#-style pipeline operator in the JavaScript parser and need help handling the await keyword correctly in pipeline expressions.

*   When the F#-style pipeline operator is used inside a non-async function, the 'await' keyword appearing as a pipeline step must be parsed as a plain identifier, not as an await expression. The resulting AST node must be an Identifier with name 'await'.

*   In an async function, when 'await' is the sole right-hand step of the pipeline operator inside a for...in variable initializer, it must be parsed as an await expression without an argument. The resulting AST must reflect a BinaryExpression with operator '|>' whose right-hand side is an AwaitExpression node.

*   When an async arrow function body uses 'await' as a pipeline step (e.g. piping into 'async () => await'), the parser must hard-throw a syntax error with message 'Unexpected token' at the position immediately after the 'await' token.

*   When 'await' is used as a solo pipeline step and is immediately followed by a binary operator other than the pipeline operator itself, the parser must produce a recoverable SyntaxError with the exact message: 'Cannot mix binary operator with solo-await F#-style pipeline. Please wrap the pipeline in parentheses.' The error must appear at the position of the 'await' token. The parser must still produce an AST (the error goes into the top-level 'errors' array).

*   This 'Cannot mix binary operator' error must be raised for any binary or logical operator following solo-await in the pipeline, including logical operators (e.g. '&&') and exponentiation ('**').

*   When 'await' appears parenthesized as a pipeline step (i.e. '|> (await)'), the parser must hard-throw a syntax error with message 'Unexpected token' at the position of the closing parenthesis.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.