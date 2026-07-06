## Description

The current API for entering a span requires passing a closure, which means the span's active period is always tied to a single closure call. This makes it difficult to write code where a span should remain active across multiple statements, through a loop body without nesting, or in contexts where closures are inconvenient (such as mutable borrow conflicts or code involving `return` and `?`).

## Expected Behavior

- There should be a way to enter a span that returns a guard value. The span should remain entered for as long as the guard is alive, and automatically exit when the guard is dropped (e.g., at the end of a block or function).
- The existing closure-based span entry should be renamed to better reflect its behavior — that it executes a provided function *within* the scope of the span — making the API more self-documenting.
- Both approaches should behave identically in terms of subscriber notifications (enter/exit), and both should support disabled spans (where entering is a no-op).
- The closure-based approach must return the result of the closure, allowing it to be used in value-returning contexts.

## Why This Matters

Developers using this tracing library in production code frequently encounter patterns where the current closure-based API is awkward or impossible to use — such as in loops, across `?` operators, or when closures would cause borrow checker issues. A guard-based API follows established Rust idioms (similar to mutex guards) and enables spans to be used naturally in any code structure.
