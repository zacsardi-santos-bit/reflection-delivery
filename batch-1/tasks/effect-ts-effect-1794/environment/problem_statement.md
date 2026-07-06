## Description

The schema library supports modeling transformations between wire formats and strongly-typed values, and already provides a way to encode/decode Either values using tagged unions. However, there is no built-in way to handle the case where the wire format is an **untagged union** — meaning a field that can be one of two types with no discriminator — and the decoded result should be an Either value.

This is a common pattern when working with APIs or data formats where a field can hold two different types, and the consumer needs to distinguish between them. Currently, developers must manually construct the transformation logic, which is error-prone and repetitive.

## Expected Behavior

- A new schema combinator should be available that accepts two schemas (one for the left/error case, one for the right/success case) and returns a schema that:
  - Decodes inputs by attempting the right schema first, then the left schema as a fallback
  - Produces a properly typed Either value as the decoded result
  - Encodes Either values back to the appropriate wire format using the respective schema
- When both schemas can decode the same input, the right case should take priority
- Proper error messages should be produced when neither schema can decode the input, or when encoding fails

## Why This Matters

Without this combinator, modeling untagged-union-to-Either transformations requires verbose manual schema construction. Adding this makes it straightforward to work with APIs that return fields of ambiguous type, and keeps schema definitions concise and expressive.
