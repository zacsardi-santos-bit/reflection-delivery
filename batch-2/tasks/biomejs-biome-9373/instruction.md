I'm using Biome's organize imports feature and I've noticed it doesn't fully handle bare exports — the kind where you export identifiers from the current file without specifying a source module.

*   When a file contains both import statements and bare export statements (exports without a 'from' source clause) that are not separated by a blank line, the organizeImports action must report a diagnostic with message 'The imports and exports are not sorted.' and apply a safe fix labeled 'Organize Imports (Biome)' that inserts a blank line between the import block and the bare export block.

*   When multiple consecutive bare export statements appear in the same chunk (not separated from each other by non-export code), the organizeImports action must merge them into a single export statement with all specifiers combined and sorted alphabetically. For example, 'export { a }; export { c }; export { b, d };' becomes 'export { a, b, c, d };'.

*   When custom group ordering options are provided via a '.options.json' file with the 'groups' configuration under 'assist.actions.source.organizeImports.options', the organizeImports action must apply that ordering to export statements. A group with 'type: true' must place type-only exports (e.g., 'export type { T }') before value exports. A ':NODE:' group specifier must group exports re-exporting from Node.js built-in modules (e.g., 'node:path'). Groups without a matching specifier are placed after all specified groups.

*   Bare exports that are separated from sourced exports by non-export statements (such as function declarations) must remain in their own separate chunk and not be merged with the sourced export chunk.

*   The test fixture file previously named 'unsorted-from-less-export.js' must be renamed to 'unorganized-bare-export-specifiers.js', with the corresponding snapshot file also renamed and its internal 'expression' and diagnostic location references updated to match the new filename.


*   Interface details: NO INTERFACES NEEDED

The tests for this task are snapshot-based spec tests. They do not import or call specific Rust functions by name. Instead, they validate the behavior of the `organizeImports` assist action by providing input fixture files (`.js`/`.ts`) alongside expected output snapshot files (`.snap`) and optional option configuration files (`.options.json`). The test infrastructure automatically runs the action against the fixture and compares the output to the snapshot.

The test files that must be present and contain the correct content are:

- `crates/biome_js_analyze/tests/specs/source/organizeImports/chunk-with-bare-export.js`
- `crates/biome_js_analyze/tests/specs/source/organizeImports/chunk-with-bare-export.js.snap`
- `crates/biome_js_analyze/tests/specs/source/organizeImports/custom-order-exports.options.json`
- `crates/biome_js_analyze/tests/specs/source/organizeImports/custom-order-exports.ts`
- `crates/biome_js_analyze/tests/specs/source/organizeImports/custom-order-exports.ts.snap`
- `crates/biome_js_analyze/tests/specs/source/organizeImports/mergeable-bare-exports.js`
- `crates/biome_js_analyze/tests/specs/source/organizeImports/mergeable-bare-exports.js.snap`
- `crates/biome_js_analyze/tests/specs/source/organizeImports/unorganized-bare-export-specifiers.js` (renamed from `unsorted-from-less-export.js`)
- `crates/biome_js_analyze/tests/specs/source/organizeImports/unorganized-bare-export-specifiers.js.snap`
- `crates/biome_js_analyze/tests/specs/source/organizeImports/unsorted_export_chunk.js` (modified)
- `crates/biome_js_analyze/tests/specs/source/organizeImports/unsorted_export_chunk.js.snap` (modified)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.