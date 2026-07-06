## Description

When Gleam code using pipeline expressions is compiled to JavaScript, the compiler omits semicolons from intermediate pipeline steps that are used as statements. This causes the generated JavaScript to be syntactically ambiguous — specifically, if the statement following a pipeline begins with a curly brace, JavaScript engines will misinterpret it as part of the previous expression rather than a new, independent statement. This can silently change the runtime behavior of the compiled program.

## Expected Behavior

- When a pipeline expression is used as a statement in a function body (not the final return value), each step generated as a JavaScript statement must be terminated with a semicolon.
- When a debug-print keyword appears as an intermediate pipeline step, the generated call for it must end with a semicolon.
- The generated JavaScript must be unambiguous regardless of what statement follows the pipeline.

## Example

If a pipeline is followed by a block expression on the next line, the compiled JavaScript should treat them as separate statements. Without semicolons, the JavaScript engine may incorrectly interpret the block on the next line as a continuation of the pipeline expression — for example, treating it as a function call argument — producing incorrect behavior.

## Why This Matters

This is a correctness issue in the JavaScript compilation target. The generated JavaScript may silently produce wrong results depending on the surrounding code, making it a hard-to-diagnose bug for users whose programs compile without errors but behave unexpectedly at runtime.
