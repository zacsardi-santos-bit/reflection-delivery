## Description

The type checker's intersection simplification is incomplete for certain combinations of a string literal type with truthiness constraints. Specifically, when a string literal type is intersected with a constraint that the value must be "always truthy," the simplification should recognize that the only falsy inhabitant of the string literal type is the empty string, and thus narrow the result to the string literal type excluding the empty string. Currently, this simplification is not performed for all orderings and combinations of these constraints.

## Expected Behavior

- Intersecting a string literal type with an "always truthy" constraint should simplify to the string literal type with the empty string excluded.
- Intersecting a string literal type with a negation of "always falsy" should produce the same simplification as above (since "not always falsy" is equivalent to "always truthy" for string literal values).
- When an "unknown" type is also present in the intersection alongside a string literal type and a falsy constraint (or a negation of truthy), the simplification should preserve the unknown component while narrowing the string part to just the empty string.

## Why This Matters

These incomplete simplifications cause the type checker to report less precise types than it should, making it harder to reason about the truthiness of string values in type-checked Python code. Fixing this ensures consistent and correct type narrowing regardless of the order in which the intersection members appear or whether an unknown type is also present.
