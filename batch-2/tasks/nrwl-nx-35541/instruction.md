I'm working on the devkit package which is about to introduce a strict exports map.

*   The migration file must be located at packages/devkit/src/migrations/update-23-0-0/update-deep-imports.ts and export: a default async migration function, a named function `rewriteDevkitDeepImports`, and a named constant `DEVKIT_INTERNAL_SYMBOLS`.

*   DEVKIT_INTERNAL_SYMBOLS must be an exported, iterable constant (e.g. a ReadonlySet<string>) containing the names of symbols that belong to the `@nx/devkit/internal` subpath. It must include at minimum: `dasherize`, `classify`, `camelize`, `addPlugin`, `FileExtensionType`. The symbol `names` must NOT appear in this set.

*   rewriteDevkitDeepImports must accept a TypeScript source string and return a new string. For named imports from any `@nx/devkit/src/...` path: symbols whose names appear in DEVKIT_INTERNAL_SYMBOLS must be rewritten to import from `@nx/devkit/internal`; symbols whose names do not appear in DEVKIT_INTERNAL_SYMBOLS must be rewritten to import from `@nx/devkit`.

*   When a single named import contains a mix of internal and public symbols, rewriteDevkitDeepImports must split it into two separate import declarations — one for `@nx/devkit` and one for `@nx/devkit/internal`.

*   rewriteDevkitDeepImports must preserve `as` alias syntax (e.g. `{ dasherize as toKebab }`) in the output import declaration.

*   rewriteDevkitDeepImports must handle `import type` declarations, preserving the `type` keyword in the output. It must also preserve inline `type` modifiers on individual specifiers (e.g. `{ type FileExtensionType, addPlugin }`). When the parent import is already `import type`, redundant inline `type` modifiers on individual specifiers must be dropped.

*   rewriteDevkitDeepImports must handle multi-line named imports (formatted across multiple lines), normalizing them into a single-line rewritten declaration.

*   rewriteDevkitDeepImports must process all deep-import statements in a single source string, rewriting each one.

*   For import forms that cannot be split by symbol — side-effect imports (`import '...'`), default imports, namespace imports (`import * as`), `require(...)` calls, and dynamic `import(...)` — rewriteDevkitDeepImports must fall back to rewriting the module specifier to `@nx/devkit/internal`, preserving the rest of the import shape. Original quote style (single or double quotes) must be preserved.

*   rewriteDevkitDeepImports must leave imports that are already targeting `@nx/devkit` or `@nx/devkit/internal` (i.e. not deep paths) completely unchanged.

*   After rewriting, rewriteDevkitDeepImports must collapse duplicate named import declarations targeting the same specifier and type-onlyness. Rewritten imports must be merged into any pre-existing `@nx/devkit` or `@nx/devkit/internal` import. Multiple deep-import rewrites that resolve to the same target must be collapsed into one declaration. Duplicate specifier names must appear only once in the merged declaration. Value imports and type-only imports must NOT be merged with each other. Unrelated imports between declarations must be preserved.

*   The default export migration function must accept an Nx Tree and scan all `.ts`, `.tsx`, `.cts`, and `.mts` files in the workspace. For each file that contains the `@nx/devkit/src/` prefix, it must apply rewriteDevkitDeepImports and write the result back. Files without that prefix must not be modified. Non-TypeScript files (e.g. `.md`) must not be modified.


*   Interface details: Type: Function
Name: rewriteDevkitDeepImports
Location: packages/devkit/src/migrations/update-23-0-0/update-deep-imports.ts
Signature: rewriteDevkitDeepImports(source: string): string
Description: Pure function that takes TypeScript source code as a string and returns a rewritten string. Rewrites any import/require/dynamic-import statements that reference `@nx/devkit/src/...` deep paths to canonical entry points (`@nx/devkit` for public symbols, `@nx/devkit/internal` for internal symbols). Also collapses duplicate import declarations for the same specifier. Does not modify non-deep-import statements.

Type: Constant
Name: DEVKIT_INTERNAL_SYMBOLS
Location: packages/devkit/src/migrations/update-23-0-0/update-deep-imports.ts
Signature: DEVKIT_INTERNAL_SYMBOLS: ReadonlySet<string>
Description: Exported iterable constant (a Set of strings) listing every symbol name that should be routed to `@nx/devkit/internal` rather than `@nx/devkit`. Must include at minimum: `dasherize`, `classify`, `camelize`, `addPlugin`, `FileExtensionType`. The symbol `names` must NOT be in this set (it is a public symbol routed to `@nx/devkit`).

Type: Function
Name: default export (migration)
Location: packages/devkit/src/migrations/update-23-0-0/update-deep-imports.ts
Signature: default async function(tree: Tree): Promise<void>
Description: Nx workspace migration function. Iterates over all files in the virtual filesystem (Tree), processes only `.ts`, `.tsx`, `.cts`, and `.mts` files that contain the `@nx/devkit/src/` prefix, applies rewriteDevkitDeepImports to each, and writes back any changed content.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.