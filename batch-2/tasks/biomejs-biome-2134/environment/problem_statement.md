## Description

The code formatter does not correctly indent deeply nested conditional expressions when inline comments are present on the operator tokens. Similarly, TypeScript type declarations that use union types inside the properties of object type members are also formatted incorrectly.

## Expected Behavior

- When a conditional expression is nested inside the branch of another conditional expression, and each operator has an inline comment, the formatter should apply proper incremental indentation at every nesting level. Each level of nesting should produce a clearly indented block that is visually distinguishable from the outer level.
- Inline comments on operator tokens should be preserved on the same line as their operator in the formatted output.
- When a TypeScript type alias uses a union type, and one of the union members is an object type whose properties are themselves typed as union types, the formatter should correctly indent all levels of the nested structure.

## Why This Matters

Developers who write deeply nested conditional expressions with explanatory comments on each branch operator, or who model complex data shapes using nested TypeScript union types, currently receive malformed output. This makes the formatted code harder to read and may even change its visual structure in ways that misrepresent the logic. Fixing this ensures the formatter reliably handles these common real-world patterns.
