## Description

The parser silently accepts binding-with-else statements where the initializer expression is a struct literal without emitting any diagnostic. Because struct literals are delimited by curly braces, their closing brace appears immediately before the else keyword — a construct that should be flagged as disallowed.

Currently, writing a binding statement where the right-hand side is a struct literal followed by an else block is parsed without any error, even though this pattern is not valid Rust.

## Expected Behavior

- When the parser encounters a binding-with-else statement whose initializer is a struct literal, it should emit a clear error indicating that a right curly brace before else in such a statement is not allowed.
- The error should be reported at the position of the closing brace of the struct literal.
- The parser should still recover from the error and produce a useful parse tree, rather than abandoning the parse entirely.

## Why This Matters

Without this diagnostic, developers can write syntactically ambiguous code involving struct literals in binding-with-else statements and receive no feedback from the parser. Emitting a proper error helps users understand why this construct is disallowed and enables downstream tooling (such as IDE diagnostics) to surface the problem accurately.
