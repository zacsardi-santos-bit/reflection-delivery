## Description

The floating-promises lint rule fails to detect unhandled promises when a function's return type is expressed as a conditional type alias — a TypeScript pattern where the return type is written as a conditional expression that evaluates to different types depending on type parameters.

For example, a type alias that says "if some condition holds, the result is a Promise; otherwise, the result is something else" would cause the lint rule to silently ignore calls to functions with that return type, even when the Promise is never awaited or handled. This creates a blind spot where real floating-promise bugs go unreported.

## Expected Behavior

- When a function's return type is a conditional type alias and at least one branch of the condition is a Promise type, the lint rule should recognize that the function might return a Promise.
- Calls to such functions, where the return value is not awaited, chained with error handling, or explicitly discarded, should be flagged as floating promises.
- The rule should treat a conditional type alias as a union of both possible outcomes (the true branch and the false branch) rather than giving up and treating it as an unresolvable type.

## Why This Matters

TypeScript users commonly define helper types with conditional logic. If the lint rule does not look through conditional type aliases, it can miss floating-promise bugs that are real risks in production code. Developers relying on this rule for safety guarantees would get false confidence that all floating promises are caught.
