## Description

Prettier does not consistently format complex TypeScript type expressions when they appear inside generic type angle brackets and exceed the configured line width. Specifically, intersection types, operator-prefixed types, array types, and indexed access types are not being wrapped properly — they stay on one line even when they should break onto their own indented line.

This inconsistency is especially noticeable in arrow function return type annotations. When a long or complex type appears as a generic type argument in a return type position, prettier ignores the print width and keeps it inline, while simpler types (like union types) are wrapped correctly.

## Expected Behavior

- When a generic type parameter contains a complex type expression and the total line length exceeds the print width, the type argument should be broken onto its own indented line, with the closing angle bracket on a separate line.
- This should work consistently whether the generic type annotation appears on a variable declaration or on an arrow function's return type.
- Short type parameters that fit within the print width should remain on a single line without unnecessary breaking.

## Why This Matters

Inconsistent line-wrapping behavior makes the formatter unreliable for TypeScript codebases that use complex generic types. Developers expect all complex type expressions to be treated uniformly when it comes to line-length decisions. The current behavior produces output that exceeds the configured print width in some cases, depending arbitrarily on which kind of type construct is used.
