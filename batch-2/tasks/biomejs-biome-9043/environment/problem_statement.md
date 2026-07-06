## Description

Biome's CSS parser does not correctly handle Tailwind utility class declarations whose names contain a slash character. Tailwind CSS commonly uses slash notation in utility names to express modifiers or opacity variants. When a developer writes such a declaration with Tailwind support enabled, the parser fails to treat the entire name (including the slash) as a single identifier, resulting in a broken or incorrect parse tree.

## Expected Behavior

- When Tailwind directives are enabled, a utility declaration with a slash in its name should parse successfully as a valid utility rule, with the entire name (including the slash) recognized as one identifier.
- When Tailwind directives are disabled, such a declaration should produce the standard parse diagnostic indicating that Tailwind-specific syntax is not enabled, just as any other Tailwind-specific syntax would.

## Why This Matters

Slash-containing names are valid Tailwind CSS utility syntax. Developers enabling Tailwind support in Biome should be able to write utility declarations with these names without encountering parser failures. The parser should correctly accept this syntax when Tailwind directives are enabled and provide the appropriate error message when they are not.
