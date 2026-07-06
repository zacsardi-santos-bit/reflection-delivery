## Description

Several components in this codebase need to parse and normalize JSON schemas used by external tool definitions, but the only existing implementation of this logic lives inside the large central core crate. Any component that wants schema parsing today must take on the entire core as a dependency, which is too heavy.

The schema parsing logic itself is self-contained: it handles external schemas that may be missing required fields, use non-standard type representations, or rely on shorthand forms that our internal model does not directly support. This includes things like boolean-valued schemas, numeric types expressed as integers, arrays without item type specs, and objects inferred from keyword presence rather than an explicit type annotation.

## Expected Behavior

- A new, standalone crate should own the schema model and the parsing/normalization logic.
- It should be lightweight (no dependency on the central core crate).
- The schema parser should handle all the real-world edge cases that external sources produce:
  - Boolean shorthand schemas should be coerced to a permissive string schema.
  - Schemas with object-like keywords but no explicit type should be inferred as objects.
  - The "integer" type variant should be normalized to the numeric type.
  - Arrays without an item type should default to a string item type.
  - Nested schemas inside additional-properties constraints should be recursively normalized.

## Why This Matters

Extracting this logic into its own crate makes it possible for lightweight consumers to use schema parsing without pulling in the entire core dependency tree, and gives the parsing logic a clear, independently-testable home.
