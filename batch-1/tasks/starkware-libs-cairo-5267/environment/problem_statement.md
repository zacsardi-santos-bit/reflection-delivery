## Description

The Sierra code generator currently threads generated statements through function return values: every code generation function builds up a list of statements and returns it, forcing each caller to collect, extend, and pass these lists around manually. This pattern is noisy and makes it difficult to attach source location information consistently — callers have to remember to set or propagate location data on every statement they receive.

The goal is to refactor the code generation so that statements are accumulated inside the generator context itself. Rather than returning lists of statements, code generation functions should push their output directly into the shared context object. The context would then own the accumulated statement list, and callers retrieve it only once when generation is complete.

## Expected Behavior

- Code generation functions for blocks, statements, match expressions, and returns should no longer return statement lists — they should store results in the context.
- The context object should expose a method to retrieve all accumulated statements after generation is done.
- Source location information should be applied centrally when a statement is pushed to the context, rather than by each individual call site.
- Helper functions that construct individual statement values should return plain statements, with location wrapping handled by the context.

## Why This Matters

This removes a significant amount of boilerplate from the code generation layer, makes location tracking consistent, and eliminates the risk of callers forgetting to propagate location data. The resulting code is easier to follow and extend.
