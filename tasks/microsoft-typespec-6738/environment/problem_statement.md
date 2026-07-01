## Description

The C# client code generator fails to handle list operations that are annotated as pageable but don't specify any pagination mechanism (no next-link URL, no continuation token). These operations — sometimes called implicit or single-page paging — are valid: the API returns all results in a single response while still being modeled as a collection. Currently, the generator either crashes or produces incorrect code for these operations.

## Expected Behavior

- When a list operation is marked as a paging operation but defines no next-link or continuation token, the generator should produce the appropriate collection result wrapper classes — both synchronous and asynchronous, both untyped and typed variants.
- The generated wrapper classes should follow the standard pattern: store client and parameter state, yield a single page result from the raw pipeline, and return null for the continuation token (since there is no next page).
- The client method signatures for such operations should return the correct pageable collection result types.
- An operation that has no paging metadata at all should continue to be treated as a non-paging operation.

## Why This Matters

Developers defining list operations in their TypeSpec API specs sometimes model single-response collections as pageable (for consistency or future extensibility) without specifying pagination tokens. The generator should produce clean, correct client code for these cases rather than crashing or skipping pagination support.
