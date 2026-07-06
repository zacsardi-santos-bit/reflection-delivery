I've been looking at the JavaScript output Gleam produces when destructuring values through pattern matching, and I noticed it generates unnecessarily verbose code whenever it binds variables from a pattern.

*   When compiling a pattern destructuring assignment that is exhaustive and guaranteed to succeed at runtime, the generated JavaScript must use a single combined declaration-and-initialization statement (e.g., `let x = value;`) rather than two separate statements (a bare declaration followed by an assignment).

*   This combined declaration style must apply consistently across all contexts where patterns always succeed: simple let-assert with irrefutable patterns, use-expression bindings, custom type field destructuring, pattern assignments within blocks, and variable bindings in case expressions.

*   When a let-assert pattern can fail at runtime (e.g., matching against a specific variant of a sum type), the compiler must continue generating a hoisted bare declaration for the bound variable followed by a conditional assignment inside an if/else block that throws on mismatch — not a combined declaration.

*   Source maps for always-succeeding pattern destructuring must correctly track source positions when the combined declaration form is used.

*   The source map test for a pattern assertion that can fail at runtime must compile the pattern to the hoisted-declaration form and include error-throwing code with the correct metadata (file path, module, line, function name, message, and character offsets for the value, the full assertion span, and the pattern span).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.