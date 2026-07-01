## Description

Some event types in the Matrix specification are logically valid as both global account data and room account data. Currently, it is only possible to declare a content struct for a single event category at a time. This means that if the same type needs to function in both global and room account data roles, developers must either duplicate code or use workarounds.

## Expected Behavior

- A single content struct declaration should be able to specify two compatible event categories at once (specifically, both the global account data and room account data categories).
- When declared with two compatible categories, all required trait implementations, named wrappers, and enum support should be automatically generated for both categories.
- The framework should reject combinations of incompatible categories at compile time and report a clear error indicating that only account data types support the dual-category declaration.

## Why This Matters

Matrix events sometimes occupy multiple categories by design — being valid as both global and room account data. Supporting this in the macro system eliminates the need for duplication or manual workarounds, and provides the same ergonomics for dual-category types as single-category types enjoy today. Compile-time rejection of invalid combinations ensures developers get immediate feedback when attempting unsupported configurations.
