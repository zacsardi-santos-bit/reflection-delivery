## Description

When using this library to generate TypeScript type definitions from Rust types, tag field keys in the generated output are not being quoted correctly. If you annotate a Rust type with a tag attribute (to add a discriminant field to the generated TypeScript), the library currently emits the tag key as a bare unquoted identifier. TypeScript (and JSON) expect string literal keys in certain contexts, so this leads to incorrect type definitions.

Additionally, there is a separate issue where flattening one internally-tagged enum inside a variant of another internally-tagged enum does not work correctly. The resulting TypeScript is either malformed or missing the intersection type that should combine the outer variant's fields with the inner enum's union of variants. This makes it impossible to correctly represent certain common Rust data model patterns in TypeScript.

## Expected Behavior

- Tag attribute keys in generated TypeScript should always appear quoted (as string literals), not as bare identifiers.
- When a tagged enum variant flattens another tagged enum, the generated TypeScript should represent this as an intersection type: the outer variant's fields combined with the inner enum's union variants in parentheses.
- When a struct directly flattens an enum type, its TypeScript declaration should match the declaration of the flattened enum exactly.
- Internally-tagged enum variants where all named fields are skipped should not produce extra whitespace in the TypeScript output.

## Why This Matters

Developers using this library to bridge Rust and TypeScript type systems rely on accurate TypeScript output. Incorrect quoting of tag keys and broken flattening of nested tagged enums mean the generated types do not compile or behave unexpectedly on the TypeScript side, requiring manual corrections after code generation.
