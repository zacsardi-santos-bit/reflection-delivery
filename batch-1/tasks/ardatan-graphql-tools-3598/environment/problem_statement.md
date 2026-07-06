## Description

When organizing a GraphQL schema across multiple files using a "master schema" approach — where the root file contains only import directives and no type definitions of its own — the schema import system fails to include deeply nested transitive dependencies in the final resolved schema.

Specifically, if the master schema imports only a subset of types from a file, and that file in turn imports from another file, and so on through three or more levels, the resulting schema silently drops types that are needed but only reachable through those intermediate import chains.

## Expected Behavior

- A root schema file that contains only import statements (no type definitions) and selectively imports types from a downstream file should produce a complete schema.
- All transitively required types — regardless of how many levels deep they are defined — must appear in the resolved output.
- Mixed import styles (importing specific named types at some levels, importing all types via wildcard at others) must all be handled correctly across the full import chain.

## Why This Matters

Users who structure their GraphQL schemas with a master entry point and layered dependency files may unknowingly get an incomplete schema. This is especially problematic for complex schemas with shared types nested several files deep, where missing types may only surface at runtime rather than at schema build time.
