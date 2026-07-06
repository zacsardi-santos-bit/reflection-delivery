## Description

The JavaScript minifier is appending an unnecessary semicolon at the very end of every minified output. When you minify a single expression or statement, the result always ends with a trailing semicolon, even though this serves no purpose and adds an extra byte to the output.

For example, minifying a simple arithmetic expression currently produces a result that ends with a trailing semicolon instead of just the simplified value. This behavior applies across all JavaScript constructs — comparisons, logical operators, variable declarations, function expressions, arrow functions, assignments, and everything else the minifier handles.

## Expected Behavior

- Minified output should **not** include a trailing semicolon at the very end of the output.
- Semicolons between multiple statements within the same snippet should still be preserved. For instance, two consecutive statements should minify such that the semicolon between them is kept but no trailing semicolon appears at the end.
- This applies universally across all JavaScript expression and statement types.

## Why This Matters

Other popular JavaScript minifiers do not include this trailing semicolon. Having it makes our output slightly larger than necessary and inconsistent with the rest of the ecosystem. Removing it reduces output size and makes the minifier behave as developers expect.
