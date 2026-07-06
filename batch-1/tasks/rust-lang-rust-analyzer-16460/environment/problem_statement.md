## Description

In Rust, the last expression in a function body is implicitly returned, making an explicit trailing return statement redundant. Many linters flag this pattern as unnecessary, and rust-analyzer should offer a similar diagnostic so users get an in-editor warning and an automated fix.

We need a new diagnostic that detects redundant trailing return expressions and offers to remove the return keyword, turning the statement into an idiomatic tail expression. The diagnostic should cover all the places a trailing return can appear: the last statement of a function body, both branches of a trailing if/else, every arm of a trailing match, closure bodies, and nested inner function definitions.

## Expected Behavior

- A trailing explicit return at the end of a function or closure body is flagged as a weak diagnostic.
- The diagnostic fires in if/else branches and match arms when the entire control flow construct is the last thing in the function.
- The diagnostic does **not** fire when a return expression appears before other statements — only when it is the true final value.
- An automated fix is offered that removes the return keyword (and the trailing semicolon, if present), leaving just the bare expression. For a bare unit return with no value, the fix removes the statement entirely.

## Testing Infrastructure

When writing tests for this diagnostic, some test fixtures contain if/else constructs that also trigger an existing diagnostic for unnecessary else branches. A new test helper function is needed that lets tests disable specific other diagnostics for the duration of a fix check, so fix tests for trailing return can be written without those other diagnostics interfering.

## Why This Matters

Idiomatic Rust prefers implicit returns. Having an in-editor warning with a one-click fix encourages cleaner code and helps developers unfamiliar with Rust's implicit return rules adopt the convention quickly.
