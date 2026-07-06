## Description

Gleam supports a syntactic shorthand that makes callback-heavy or continuation-style code look more sequential and readable. While this shorthand is convenient to write, it can be confusing to newcomers or even experienced developers who want to understand exactly what the code compiles down to. There is currently no editor tooling to help visualize or learn the underlying form.

## Expected Behavior

A new editor code action should be available whenever the cursor is inside one of these shorthand expressions. When triggered, it should transform the shorthand into its equivalent expanded form: a standard function call where the remaining block body is wrapped in an anonymous function and passed as the last argument.

The action should handle:
- Shorthands that call functions with no arguments or with existing arguments (arguments are preserved)
- Shorthands with no bound variables, a single bound variable, or multiple bound variables (each becoming a parameter of the anonymous function)
- Bound variables that carry type annotations (annotations should be preserved)
- Nested shorthands, where the action applies to the one the cursor is currently inside

The action should **not** be offered when the shorthand uses complex destructuring patterns such as tuples or literal values, since those cannot be directly expressed as simple anonymous function parameters.

## Why This Matters

This helps developers understand the semantics of this syntactic form and makes it easier to refactor or migrate code that uses the shorthand into a style that may be clearer in certain situations.
