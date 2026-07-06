## Description

The Gleam compiler currently stops type-checking a module as soon as it encounters certain kinds of errors, such as a function with no implementation, a type annotation mismatch, an invalid external declaration, or a type definition with duplicate type parameters. This means developers only see one error per compilation run, forcing them to fix one issue, recompile, discover the next issue, and repeat.

## Expected Behavior

- When any function in a module has an error (missing implementation, body type mismatch, annotation mismatch, invalid external, or unsupported target), the compiler should continue analyzing the rest of the module and report all errors found.
- Functions that have errors in their bodies but provide type annotations should still be usable by other functions in the module for type-checking purposes. Callers should receive proper type errors rather than confusing "function not found" messages.
- When a function has no support for the current compilation target, the compiler should still be aware of the function so that call sites produce a meaningful "unsupported target" error rather than pretending the function does not exist.
- Type definitions with duplicate type parameters should report the error but not prevent subsequent type definitions from being checked.

## Why This Matters

Seeing all errors at once dramatically improves the developer experience. Instead of iterating through errors one at a time with repeated compilation cycles, developers can understand and fix everything wrong with their module in a single pass. This is especially important in larger modules where a single structural issue early in the file could otherwise hide a dozen real problems.
