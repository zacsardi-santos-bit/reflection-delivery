Implement a new lint rule named 'noBarrelFile' in the nursery category of the `biome_js_analyze` crate to detect and warn about JavaScript/TypeScript barrel files. Ensure the rule identifies re-export patterns that contribute to performance issues and large module graphs, while exempting type-only re-exports.

Requirements:
*   Create the `NoBarrelFile` rule as a Rust struct in `crates/biome_js_analyze/src/analyzers/nursery/no_barrel_file.rs`.
    *   Implement the `Rule` trait for `NoBarrelFile`.
    *   Register the rule in the nursery analyzer group.
*   Ensure the rule flags the following re-export patterns:
    *   Named re-exports: `export { foo } from '...'`.
    *   Named re-exports with aliases: `export { foo as bar } from '...'`.
    *   Default re-exported as named: `export { default as bar } from '...'`.
    *   Mixed value and type named re-exports: `export { foo, type Bar } from '...'`.
    *   Wildcard re-exports: `export * from '...'`.
    *   Wildcard namespace re-exports: `export * as ns from '...'`.
*   Exclude type-only re-export statements from being flagged:
    *   `export type * from '...'`.
    *   `export type * as ns from '...'`.
    *   `export type { foo } from '...'`.
    *   `export type { foo as bar } from '...'`.
    *   `export type { default as bar } from '...'`.
*   Produce a diagnostic with the following details when the rule fires:
    *   Category: `lint/nursery/noBarrelFile`.
    *   Severity: warning.
    *   Message: 'Avoid barrel files, they slow down performance, and cause large module graphs with modules that go unused.'
    *   Info note: 'Check this thorough explanation to better understand the context.'
    *   Highlight the full text range of the flagged export statement.
*   Place test spec input files under `crates/biome_js_analyze/tests/specs/nursery/noBarrelFile/`.
*   Ensure snapshot files (`.snap`) match the expected diagnostic output format used by the biome spec test framework.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.