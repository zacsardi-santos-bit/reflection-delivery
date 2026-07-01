## Description

The recursion compiler's virtual machine builder currently only supports a single field type parameter, which means it cannot properly represent or execute arithmetic operations over extension fields. Since recursive proof systems commonly rely on extension field arithmetic for security, this severely limits what programs can be expressed and verified using the compiler.

## Expected Behavior

- The virtual machine builder should accept both a base field type and a separate extension field type as distinct type parameters.
- Extension field values (elements of the extension field, not just the base field) should be supported as a first-class type that can be stored, loaded, and operated on in compiled programs.
- Arithmetic operations — addition, subtraction, multiplication, and division — should be supported for extension field values, and the runtime should execute them correctly.
- Assertion of equality between two extension field values should be supported, with runtime trapping if the equality does not hold.
- Existing programs that use only base field operations (conditionals, loops) should continue to work correctly with the updated two-parameter builder.

## Why This Matters

Without proper extension field support, it is impossible to compile and run programs that perform cryptographic operations involving extension field elements. Adding this support unlocks the ability to write and verify recursive programs that use the full range of field arithmetic required by modern proof systems.
