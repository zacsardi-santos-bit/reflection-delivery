## Description

The JSON schema generator for GraphQL input types has two related bugs that cause generated schemas to be incorrect or inconsistent.

**Bug 1: Wrong combinator for nullable references**

When an optional (nullable) input field references another input type, the generator currently uses the wrong JSON Schema combinator to express "this can be null or a reference to the type." This causes compatibility issues with JSON Schema validators that distinguish between the two available combinators.

**Bug 2: Incorrect handling of types used in both required and optional contexts**

When the same GraphQL input type is used as a required (non-null) field in one place and as an optional (nullable) field in another, the generator creates only a single shared definition. This is wrong: a required field must not accept null, while an optional field must. With a single shared definition, the schema cannot enforce both constraints simultaneously, leading to incorrect validation behavior.

## Expected Behavior

- Optional nullable fields that reference other input types should use the standard JSON Schema combinator for "either null or this type."
- When a type is used in both required and optional contexts, the generator should produce two separate definitions — one that allows null (for optional usage) and one that does not (for required usage). The naming convention for the non-null variant should append a suffix to distinguish it.
- When a type is used consistently (only required, or only optional), only one definition should be produced.
- The field declaration order in the GraphQL schema should not affect the output.
- These behaviors must apply consistently in arrays, recursive schemas, and complex nested structures.

## Why This Matters

Applications that use these generated schemas for input validation will incorrectly accept null values for required fields (or reject valid optional null values) due to this bug. Fixing it ensures that the generated schemas correctly enforce the nullability constraints expressed in the original GraphQL schema.
