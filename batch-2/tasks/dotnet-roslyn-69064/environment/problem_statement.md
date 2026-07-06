## Description

When developers define custom indexers, slice methods, or conversion operators on inline array structs, those members are silently bypassed by the compiler during element access and conversion operations. The compiler uses its own built-in mechanism for inline array element access instead of calling the user-defined members, but it currently provides no indication that this is happening.

This silent bypassing can lead to subtle bugs: a developer may write a custom indexer expecting it to execute, but at runtime the built-in inline array access runs instead, with no compiler feedback whatsoever.

## Expected Behavior

The compiler should emit warnings when it detects that user-defined members on an inline array type will be ignored:

- A warning should be emitted when an inline array struct defines an indexer accepting an integer index, a ranged index, or a range — since these will be bypassed in favor of the built-in element access mechanism.
- A warning should be emitted when an inline array struct defines a slice method with the standard two-integer-parameter signature, since that method will also be ignored.
- A warning should be emitted when an inline array struct defines a conversion operator to a span of the same element type, since the compiler will use its own conversion instead.

The warnings should only apply to members that are actually candidates for the built-in access path. Members with non-standard signatures, explicit interface implementations, or types that don't conflict with the built-in mechanism should not produce warnings.

## Why This Matters

Without these warnings, developers may spend significant time debugging unexpected behavior caused by their custom members being silently ignored. The warnings make the language safer and more predictable for anyone working with inline array types.
