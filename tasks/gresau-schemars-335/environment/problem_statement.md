## Description

When generating JSON schemas for Rust types that use serialization attributes to control serialization and deserialization behavior, the current schema generation does not distinguish between the two contexts. A field that is excluded from deserialization (read-only output) or excluded from serialization (write-only input) is treated the same way regardless of what the schema is being used for. Similarly, if a field or variant has different names for serialization vs deserialization (separate rename rules for each direction), the generated schema uses only one of them — making the schema inaccurate for the other context.

## Expected Behavior

It should be possible to generate two distinct schemas for the same type:

- A **deserialization schema** that reflects what the type accepts as input: fields excluded from deserialization are omitted; fields excluded from serialization (write-only) appear with appropriate markers; names use the deserialization-side rename rules.
- A **serialization schema** that reflects what the type produces as output: fields excluded from serialization are omitted; fields excluded from deserialization (read-only) appear with appropriate markers; names use the serialization-side rename rules.

This should work for all struct types (named and tuple), as well as enums with any tagging style (external, internal, adjacent, and untagged). Tuple struct schemas in each mode should reflect the actual number of elements visible in that context (compacted positions, no gaps for excluded fields).

Additionally:
- Single-value tag field constraints in adjacently-tagged enum schemas should use a more precise JSON Schema representation instead of a single-element list.
- Enums with no valid variants should produce a schema that accurately reflects that no value can match them.
- Decimal number types should accept both numeric and string JSON values, and the validation pattern should cover scientific notation.
- Fields excluded from deserialization should not appear in the default (unconfigured) schema at all — they should be fully omitted rather than shown with a read-only marker.

## Why This Matters

Users relying on schemars to generate schemas for API documentation or validation need schemas that correctly reflect the read or write contract of their types. Without this distinction, generated schemas misrepresent which fields are accepted, which are returned, and under what names — leading to incorrect API documentation and failed validation in round-trip scenarios.
