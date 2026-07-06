## Description

JavaScript's timer scheduling functions have a lesser-known but dangerous behavior: when you pass a code string as their first argument instead of a callback function, the runtime evaluates that string as executable code. This is essentially the same as using dynamic code evaluation and carries all the same risks — it can enable code injection vulnerabilities and prevents engine-level performance optimizations.

There is currently no lint rule in Biome to detect this pattern. Developers may unknowingly write code that passes strings to these timer functions in various forms: plain string literals, template strings without dynamic parts, concatenated string expressions, or calls through global object references.

## Expected Behavior

- A new lint rule in the nursery group should detect and report a diagnostic whenever a string expression is passed as the callback argument to timeout/interval/immediate scheduling functions.
- The rule should cover direct calls, calls through global objects, optional chaining, computed member access, and chained global references.
- String expressions should include string literals, template strings without substitutions, and string concatenation — even when wrapped in parentheses.
- The rule should NOT flag function arguments (function expressions, arrow functions), non-string values, template strings with dynamic substitutions, locally-shadowed function names, calls on non-global objects, or deeply-nested member chains.
- The rule should work in both plain JavaScript and JSX files.

## Why This Matters

Passing strings to timer functions is a subtle but serious mistake that creates real security vulnerabilities and performance problems. An automated lint rule gives developers immediate feedback to catch this pattern before it reaches production.
