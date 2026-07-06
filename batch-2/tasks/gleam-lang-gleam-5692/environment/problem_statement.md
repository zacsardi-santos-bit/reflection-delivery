## Description

When Gleam compiles pattern destructuring assignments to JavaScript, it currently generates two separate statements for every bound variable: first a bare variable declaration, then a separate assignment. This happens even when the pattern is guaranteed to always succeed — where the type system guarantees no other case is possible — so there is no conditional logic involved.

## Expected Behavior

- When a pattern match is exhaustive and can never fail at runtime, the compiler should produce a single combined declaration-and-initialization statement for each bound variable, rather than splitting them across two statements.
- This cleaner output should apply in all places where patterns are irrefutable: direct destructuring assignments, use-expression bindings, custom type field extractions, and pattern bindings inside blocks.
- When a pattern assertion *can* fail at runtime (because only one of several possible variants is matched), the compiler must continue generating the split form with a hoisted bare declaration and a conditional assignment with error throwing, since the variable needs to be in scope outside the conditional.

## Why This Matters

The current split form makes the generated JavaScript unnecessarily verbose and less idiomatic. Developers reading the compiled output or debugging it face more noise than necessary. Eliminating the redundant bare declarations makes the output easier to follow and matches what a JavaScript developer would naturally write.
