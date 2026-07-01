## Description

The Gleam language server currently does not include locally defined variables in its autocompletion suggestions. When a developer is editing a function body and starts typing the name of a variable they have defined earlier in the same function — whether as a function argument, a let binding, or a pattern match result — the editor offers no suggestion for it.

This is a noticeable gap: module-level values, imported names, and type constructors all appear in completions, but locally scoped variables do not. This makes in-function editing less productive.

## Expected Behavior

- Function parameters should appear as completion candidates within the function body.
- Variables bound by let-binding statements before the cursor position should appear as completion candidates.
- Variables bound after the cursor position should not be suggested.
- Pattern match bindings (including aliased patterns and destructuring patterns for tuples, lists, bit arrays, and string prefixes) should appear as completions within their respective scopes.
- Anonymous function parameters should only appear as completions inside that anonymous function's body, not in any enclosing scope.
- Variables whose names begin with an underscore (discard bindings) should not appear as completions.
- No local variable completions should be offered when the cursor is positioned within a function's parameter list declaration itself.
- Each local variable completion should show the variable's inferred type.

## Why This Matters

Without local variable completions, developers lose one of the most basic productivity features of a language server inside function bodies. Adding this closes a significant usability gap, especially for functions with many parameters or complex pattern destructuring.
