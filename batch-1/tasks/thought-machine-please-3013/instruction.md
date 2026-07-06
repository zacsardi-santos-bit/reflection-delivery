Update the lexer to support modern octal literal notation in the build system's configuration language. Ensure that both modern and legacy octal notations are correctly recognized and processed as integer tokens.

*   Modify the `nextToken` function in `src/parse/asp/lexer.go`:
    *   Recognize octal literals with the modern "0o" prefix notation.
    *   Detect the 'o' character following a leading '0' and process the subsequent octal digits as an integer token.
    *   Ensure the resulting token value for modern octal literals excludes the "0o" prefix (e.g., '0o604' becomes '0604').
    *   Continue supporting legacy octal literals (e.g., '0604') as integer tokens with the full literal value, including the leading zero.

*   Ensure both modern and legacy octal literals:
    *   Are returned as tokens of type `Int`.
    *   Have their source position correctly tracked, including line number, column number, and byte offset.

*   Use the `Int` constant from `src/parse/asp/lexer.go` (or `tokens.go` / `types.go` in the same package) to represent integer literal tokens.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.