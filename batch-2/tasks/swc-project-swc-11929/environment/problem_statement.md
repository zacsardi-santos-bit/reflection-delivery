## Description

The SWC parser's Flow type support is missing the ability to handle "bare renders type" annotations. In Flow's type system, developers can annotate a value as rendering a specific component type using a dedicated rendering annotation keyword followed directly by a type reference. This bare form — where the keyword appears directly before a type reference — is valid Flow syntax and is commonly used in React codebases.

Currently, the SWC parser fails when it encounters this syntax in any type position. This makes it impossible to parse Flow-typed code that uses bare renders type annotations.

## Expected Behavior

- Bare renders type annotations in a type position should parse successfully, with the annotation keyword consumed and discarded, and the result being the underlying type reference in the AST
- The nullable form (with a question mark placed between the keyword and the type reference) should parse as a union type that includes the referenced type along with the two nullability primitives
- The syntax should be supported in all type positions:
  - Type alias declarations
  - Function parameter type annotations
  - Object/type literal property type annotations
  - Arrow function return type annotations
  - Generic type arguments
  - Component declaration return type annotations

## Why This Matters

Many real-world Flow-typed React codebases use this syntax to express component render contracts. Without this support, SWC cannot parse such code, blocking adoption for projects that use bare renders type annotations. Fixing this unblocks an entire category of valid Flow code from being processed by SWC.
