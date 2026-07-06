## Description

The WGSL shader parser can crash (likely with a stack overflow) when given shader source code that contains extremely deep nesting — for example, thousands of levels of nested function calls or thousands of levels of nested type parameter expressions. Instead of emitting a useful error message, the process aborts ungracefully.

## Expected Behavior

- When the WGSL parser encounters nesting that exceeds a reasonable depth limit, it should stop parsing and report a clear, human-readable error indicating that the parser recursion limit was exceeded.
- This should apply both to deeply nested expression constructs (such as chained function calls) and to deeply nested type/template argument constructs.
- The error should be reported as an internal front-end error with a note describing the cause.

## Why This Matters

Without this protection, any sufficiently deeply nested (or adversarially crafted) WGSL shader can crash the compilation pipeline entirely. Adding a depth limit allows the parser to fail gracefully with an actionable error message rather than an uncontrolled crash.
