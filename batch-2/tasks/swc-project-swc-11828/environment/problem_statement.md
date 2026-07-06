## Description

There are two related bugs in how TypeScript type annotations are handled: one in the parser and one in the fast type-stripping tool.

**Parser bug**: The two TypeScript keywords for annotating expression types — the one that widens a type and the one that checks type satisfaction — should behave identically in terms of operator precedence when parsing binary expressions. However, the parser currently applies inconsistent precedence rules, causing the satisfaction keyword to be parsed at a different binding strength than the widening keyword. For example, an expression combining addition, multiplication, the satisfaction annotation, and division should parse the same way as the equivalent expression using the widening keyword — with the annotation binding the preceding expression and the subsequent division applying to the whole annotated result. This is currently broken for the satisfaction keyword.

**Fast-strip tool bug**: The fast type-stripping mode removes TypeScript type annotations by deleting annotation tokens from the source text without re-parsing. This approach has a silent correctness hazard: if a type assertion annotates an expression that includes lower-precedence binary operators, removing the annotation text changes how the remaining operators group, producing JavaScript with different runtime semantics. Neither the type-widening assertion variant nor the satisfaction-checking assertion variant currently report an error in this case — they silently emit broken code.

## Expected Behavior

- The satisfaction-checking keyword must be parsed at the same operator precedence level as the type-widening keyword in all binary expression contexts.
- The fast-strip tool must detect when removing a type assertion would change binary operator grouping (due to precedence differences or right-associativity), and must emit an unsupported-syntax error indicating that the annotation cannot be safely removed in strip-only mode.
- The fast-strip tool must continue to handle safely-removable type assertions (same operator, lower-precedence next operator, equal-tier cross-operator with parenthesized subexpression) without emitting errors.

## Why This Matters

Without this fix, code using the satisfaction-checking keyword in binary expressions is silently parsed incorrectly, and certain patterns produce semantically different JavaScript when stripped of type annotations in fast-strip mode. Both issues can result in runtime bugs that are hard to trace.
