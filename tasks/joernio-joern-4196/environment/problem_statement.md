## Description

The C# frontend generates a code property graph where type names for primitive types use short-form language aliases rather than their fully qualified runtime equivalents. For example, built-in numeric and text types appear in their abbreviated C# forms in method signatures, return types, and variable type fields throughout the graph, instead of using the consistent fully qualified names that other parts of the graph already use.

This inconsistency means that queries written against the graph must account for multiple representations of the same type, making analysis less reliable and harder to maintain.

## Expected Behavior

- All type references in the generated graph — including method return types, call node types, method signatures, and identifier types — should use fully qualified type names consistently.
- Variables declared with nullable types (using the "?" annotation) should have their type recorded without the nullable marker, so queries match the base type regardless of nullability.
- Generic collection types should be represented by their base name without embedded generic parameters.
- The API for processing parsed AST files should not require callers to construct and pass a separate type map object; the necessary type information should be resolved internally.

## Why This Matters

Inconsistent type naming makes it difficult to write cross-cutting queries that reliably find all usages of a given type. Requiring callers to manually construct and pass auxiliary type-mapping objects adds unnecessary complexity to the frontend's API and couples callers to internal implementation details.

Additionally, the resolution of inherited method calls (where a subclass calls a method defined on its parent without an explicit receiver) was incorrectly computing the method's full name, making call graph edges point to the wrong fully qualified method.
