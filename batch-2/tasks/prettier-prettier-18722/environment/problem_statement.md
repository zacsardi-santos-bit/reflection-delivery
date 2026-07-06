## Description

Prettier does not format arrow function expressions inside Angular HTML templates. When an Angular template contains an arrow function — whether in a variable binding, an interpolation expression, or an event handler — the formatter leaves it completely untouched rather than normalizing whitespace and applying the configured style.

This means extra spaces, inconsistently spaced arrow tokens, and redundant parentheses are all preserved as-is. It also means that the option controlling whether to always include or optionally omit parentheses around single-parameter arrow functions has no effect inside Angular templates.

Similarly, the type-check operator used in Angular interpolations is not properly formatted — unnecessary parentheses are kept and extra internal whitespace is not cleaned up.

## Expected Behavior

- Arrow functions anywhere in an Angular template should be formatted consistently, with extra whitespace removed.
- The option to omit parentheses around single-parameter arrow functions should be respected inside Angular templates.
- When the default parentheses style is in effect, bare single-parameter arrow functions in event bindings should have parentheses added.
- When content is too wide, arrow function parameter lists should be split to multiple lines, one parameter per line.
- Even when the "add trailing commas everywhere possible" option is enabled, arrow function parameters in Angular templates should not receive a trailing comma.
- The type-check operator in Angular interpolations should have redundant parentheses removed and whitespace normalized.

## Why This Matters

Angular templates increasingly use arrow functions in variable declarations, event handlers, and computed expressions. Without proper formatter support, prettier silently skips these constructs, leaving them inconsistently formatted even when the rest of the template is cleaned up. Developers relying on prettier for consistent Angular template formatting cannot trust it to handle modern Angular syntax.
