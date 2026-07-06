## Description

When generating JSON schemas for Rust enums that use the untagged representation, the library currently always inserts the Rust variant name as a title field into each variant's subschema within the anyOf array. This happens unconditionally, regardless of whether the consumer wants those titles.

In most cases this is undesirable: the variant names are Rust-specific identifiers that don't carry useful meaning to schema consumers, and they clutter the generated schema unnecessarily.

## Expected Behavior

- By default, untagged enum variant subschemas should **not** include a title field. The generated schema should be clean with no automatically-injected variant names.
- Developers who do want variant names visible as titles (e.g., for human-readable documentation or tooling) should be able to opt in via a new configuration option on the schema generation settings.
- When the opt-in is enabled, every variant subschema in the anyOf array must contain a title field whose value is the variant's name.
- This behavior should apply consistently across all untagged enum contexts, including enums that mix tagging strategies (e.g., untagged variants inside an otherwise-tagged enum).

## Why This Matters

Schema consumers — including validators, documentation generators, and API clients — receive cleaner, less noisy schemas by default. Teams that want the extra metadata for display or documentation purposes retain the ability to enable it explicitly rather than having no choice.
