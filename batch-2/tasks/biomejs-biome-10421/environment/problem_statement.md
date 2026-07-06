## Description

The SCSS parser does not correctly support variable interpolation in all positions within media and container query range conditions. Specifically, when a container query uses interpolated variables as both the left and right bounds of a range comparison (e.g., checking whether a property lies between two dynamic values), the parser fails to recognize this as valid syntax. This means valid SCSS code is rejected or produces incorrect parse output.

Additionally, when an interpolated value appears on the left side of a comparison in a media query range (with the property name on the right), the parser assigns the wrong node type. Instead of treating this as a distinct "reversed" form of a range query, it produces the same node type as a standard range query, which is incorrect.

## Expected Behavior

- A container range query that uses interpolated variables for both bounds should parse successfully with no errors and produce the correct AST structure.
- When an interpolated feature name is followed by a colon but no value (in a media query), the parser should report a clear error indicating an unexpected character and listing the expected value types.
- When a range interval is missing its right-side value after a comparison operator (e.g., the closing parenthesis appears where a value should be), the parser should report a clear error with the same guidance.
- A range query where the interpolated value appears on the left and the property name appears on the right should be classified as a reversed-range query — distinct from the standard form.

## Why This Matters

Developers writing SCSS for complex responsive designs often use variable interpolation to drive media or container breakpoints dynamically. Without these fixes, valid SCSS that uses interpolation on both sides of a range comparison is rejected, and malformed range queries do not produce clear error messages to guide debugging.
