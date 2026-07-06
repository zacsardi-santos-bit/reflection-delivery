## Description

When Python source code contains f-strings with syntax errors — such as unclosed expression braces or unterminated string bodies — the parser exhibits two related problems:

1. **Incorrect grouping of malformed f-strings**: Multiple f-strings on separate lines that each have an unclosed brace are merged into a single concatenated expression in the AST, instead of being treated as independent statements. This produces a wrong AST structure and incorrect byte-range information.

2. **Poor error recovery in call-argument contexts**: When an unterminated f-string appears as an argument inside a function call (for example, as part of a conditional expression), and the following line is indented code, the parser fails to recover gracefully. It produces confusing error messages pointing to the wrong locations and misidentifies what the subsequent indented block belongs to.

## Expected Behavior

- Each f-string with a syntax error should be represented as its own independent expression statement in the AST.
- Error diagnostics should accurately identify the location and nature of each individual f-string error, not the location of subsequent unrelated code.
- When an unterminated f-string appears in a function call argument context followed by an indented block, the parser should recover correctly and continue parsing the rest of the file without cascading errors.
- Spurious secondary errors (reported for code that is itself valid, but follows an invalid f-string) should not be emitted.

## Why This Matters

Developers relying on the parser for linting, formatting, or editor tooling see misleading error messages and incorrect source locations when their f-strings have syntax errors. Fixing this makes the tool significantly more useful for error feedback during development.
