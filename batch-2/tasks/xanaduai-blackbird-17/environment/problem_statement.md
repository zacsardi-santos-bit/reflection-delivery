## Description

The Blackbird Python library currently uses a third-party numerical array library as the internal representation for arrays throughout the system — in variable storage, expression evaluation, and program serialization. This creates an implicit type dependency that makes the code harder to maintain and leads to subtle bugs: certain arithmetic operations on array variables fail or produce wrong results depending on the order of operands, and there is no clear distinction between what array types are valid input to the serializer.

## Expected Behavior

- A dedicated utility function should accept a plain Python nested list (rather than a library-specific array object) and convert it to the correct Blackbird script array declaration format, detecting the element type automatically (integer, float, or complex).
- If a list with an unsupported element type is passed to this function, it should raise a clear error indicating an unsupported type.
- The program serializer should accept array arguments represented as plain Python lists and correctly produce the corresponding array declarations.
- When the serializer encounters an incompatible array type (a raw numerical-library array rather than a Python list) in an operation's arguments or keyword arguments, it should raise an error indicating an unknown argument type.
- Arithmetic operations on array variables — including subtraction, division, and exponentiation — should produce correct results regardless of whether the array variable appears on the left or right side of the operator.

## Why This Matters

Shifting from a numerical-library array type to plain Python lists as the canonical internal format makes the system more portable and removes a hard external dependency. It also makes error messages more actionable when the wrong type is passed to the serializer, rather than silently producing malformed output.
