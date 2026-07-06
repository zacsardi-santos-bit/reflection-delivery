## Description

The OpenAPI-to-GraphQL schema converter does not support APIs that use vendor-specific JSON content types in their response definitions. Some APIs — particularly travel and hospitality APIs — use a namespaced content type identifier (such as a vendor-prefixed JSON variant) rather than the bare standard JSON content type. Because the converter only recognizes the plain JSON content type exactly, it silently ignores response schemas for these APIs, resulting in a GraphQL schema with no useful types or query operations generated.

## Expected Behavior

- The converter should recognize any content type that identifies itself as a JSON variant (i.e., any content type whose name ends in "json") as a JSON response body
- An OpenAPI specification for the Amadeus Hotel List API (which uses a vendor-specific JSON content type) should be successfully parsed into a complete GraphQL schema
- The resulting schema should include all hotel search query operations, the relevant enum types for amenities, hotel source, and radius unit options, and the hotel data object types

## Why This Matters

APIs that follow the JSON API specification or use similar vendor-prefixed content types are completely skipped by the current parser, making it impossible to integrate them through Grafbase's OpenAPI connector. Adding support for this broader content type matching unlocks a whole class of APIs that could otherwise not be used.
