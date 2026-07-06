## Description

Many JavaScript and TypeScript projects use documentation comments to declare the intended visibility of exported symbols — some exports are meant to be used only within the same folder, while others are truly private and should not be accessed by any other file. However, the linter currently has no way to enforce these annotations, so they are purely informational. Any file can import any export regardless of what the documentation says.

We need a new lint rule in the correctness category that actually reads these visibility annotations and reports violations when a symbol is imported from a location that exceeds its declared access level.

## Expected Behavior

- Symbols annotated as private in their documentation comments should not be importable by any other file; attempting to do so should produce a clear diagnostic pointing to the offending import specifier.
- Symbols annotated as package-level (available only within the same directory) should be importable from within the same folder but should produce a diagnostic when imported from outside.
- Symbols annotated as public should always be importable without restriction.
- When a symbol is re-exported through a directory's index file, that re-export widens the scope — consumers who import via the index path should be allowed.
- Within a folder, importing a private symbol from that folder's own index file should be permitted.
- The rule should also handle default imports and combined imports — not just named imports.
- An explicit annotation should always take precedence over any configured default.
- The rule should support a configuration option to set the default visibility for symbols that have no annotation, so teams can treat unannotated exports as package-private across the whole project.

## Why This Matters

Without this rule, documentation-level visibility annotations have no enforcement, which means they can easily drift out of sync with actual import patterns. With this rule, teams can define clear module boundaries and get immediate feedback when those boundaries are violated.

The existing experimental rule for this purpose was too limited — it only considered whether a module was inside a subdirectory, without understanding symbol-level annotations at all. This upgrade replaces it with a much richer, annotation-aware rule in the stable correctness category.
