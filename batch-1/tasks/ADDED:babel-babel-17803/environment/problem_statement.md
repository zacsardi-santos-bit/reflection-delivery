## Description

The F#-style pipeline operator has a special feature allowing asynchronous waiting to be used as a solo pipeline step. However, several edge cases around this behavior are either unhandled or incorrectly accepted:

1. **Mixing solo-await with binary operators is silently allowed when it shouldn't be.** Writing code that pipes a value into an awaited step and then immediately applies a binary operator on the same line (e.g. with a logical or exponentiation operator) is ambiguous and should produce a clear error directing developers to add parentheses.

2. **Parenthesized await in a pipeline step should be rejected.** Parenthesizing the await expression in this context is not valid syntax and should produce a parse error.

3. **Await inside an async arrow function used as a pipeline step should be rejected.** This pattern is also disallowed but currently not properly caught.

4. **Non-async context: await should parse as an identifier.** When a pipeline expression appears in a non-async function, the keyword that would otherwise be treated as an async-await expression should instead be treated as a plain identifier.

5. **Async context with for-in: await should parse correctly.** In an async function, when a pipeline step using await appears inside a for-in variable initializer, it should be parsed as an await expression without an explicit argument.

## Expected Behavior

- Mixing a solo-await pipeline step with binary operators should produce a recoverable parse error with a message indicating the pipeline should be wrapped in parentheses.
- Parenthesized await in a pipeline step should produce a hard parse error.
- Await inside an async arrow function used as a pipeline step should produce a hard parse error.
- In non-async contexts, await in a pipeline expression should be treated as a plain identifier.
- In async contexts within for-in initializers, await should be parsed as an await expression.

## Why This Matters

These restrictions ensure that code using the F#-style pipeline operator with async/await patterns is unambiguous and that developers receive clear guidance when they write code that could be misinterpreted.
