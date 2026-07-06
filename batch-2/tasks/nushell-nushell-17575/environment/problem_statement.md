## Describe command produces nested union types instead of flat ones

## Description

When using nushell's built-in type-inspection command on a table that has columns containing values of different types, the output sometimes contains redundantly nested union type expressions. Instead of showing a clean, flat union of all distinct types, the result shows a union type wrapping another union type. This makes the type output confusing and incorrect.

The problem is especially apparent when rows with a simpler type appear before rows with a more complex nested record type. In that ordering, the type inference logic fails to properly flatten the union — it accumulates a nested union rather than merging all variants into one.

Additionally, glob-pattern values and plain string values in the same column are being incorrectly merged into a single type rather than kept as two distinct types in the union. This loses the distinction between the two value kinds.

## Expected Behavior

- Type inspection of a mixed-type column must always produce a flat union — nested union types should not appear in any output
- The flattening behavior must be consistent regardless of the order rows appear in the table
- Glob values and plain string values must remain distinguishable in the union output rather than being collapsed
- The order of types in the union should reflect the order in which they were first encountered across the rows

## Why This Matters

Accurate type descriptions are essential for users to understand and reason about the shape of their data. Nested or incorrectly collapsed type unions are misleading and may cause confusion when working with typed pipelines or debugging data shapes.
