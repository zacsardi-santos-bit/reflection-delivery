## Description

The HTML parser crashes when parsing Svelte list-rendering blocks that use method calls or function calls as the iteration key. Any time the key expression (the value in parentheses at the end of the block opening) itself contains parentheses — such as calling a method on the iterated item or passing the item to a helper function — the parser misinterprets the inner closing parenthesis as the end of the key expression. This causes a cascade of parse errors for the rest of the file.

## Expected Behavior

- Svelte list-rendering blocks with a method call as the key expression (e.g., calling a string-conversion method on the item) should parse successfully with no errors.
- Svelte list-rendering blocks with a function call as the key expression (e.g., passing the item to a key-generation function) should also parse successfully with no errors.
- In both cases the full call expression — including its own parentheses — should be captured as the key content.

## Why This Matters

These patterns are idiomatic Svelte and commonly appear in real-world templates. Rejecting them with spurious parse errors means the formatter and linter cannot process files that are perfectly valid Svelte code, blocking adoption for any project that relies on these patterns.
