## Description

When bundling with multiple entry points and entry signatures disabled (set to "false"), there is a bug in how exports are handled for entry modules that are also targeted by dynamic imports.

Specifically, if an entry module is loaded both as a static entry point and via a dynamic import from another module, the bundler incorrectly omits its exports from the output. This means the dynamic importer cannot access the exported values at runtime, leading to broken bundles.

## Expected Behavior

- An entry module that is also a target of a dynamic import should retain its exports in the output, since the dynamic importer needs to access them.
- An entry module that is only a static entry point (not dynamically imported) should not be forced to retain exports — its content may be merged into a shared helper chunk.
- When two or more entries reference the same source module, that shared content should be deduplicated into a single chunk rather than duplicated across entries.

## Why This Matters

This bug affects real-world applications that use code-splitting with dynamic imports, where some bundles serve both as entry points and as lazily-loaded modules. With entry signatures disabled, the bundler's aggressive optimization incorrectly strips away exports that dynamic importers depend on, breaking runtime behavior.

The correct behavior is that the "entry signatures disabled" mode should only suppress exports for entry points that are purely static — not for entries that double as dynamic import targets.
