# TypeORM v1 Codemod Misses Several Real-World Migration Patterns

## Description

The TypeORM migration codemod is designed to automatically update codebases from the old TypeORM API to the new version. In practice it works well for straightforward imports, but there are several common patterns it currently fails to handle.

## Missing Behaviors

- **Barrel / re-export files**: When a project has barrel or index files that re-export deprecated symbols from typeorm (or from typeorm sub-paths), the codemod doesn't rename or strip those exports the way it handles regular import declarations. This includes symbols that have been renamed, symbols that have been removed entirely, and symbols that now need to be sourced from a different package.

- **Computed / bracket-notation method calls**: When a deprecated method is called using bracket property access instead of dot notation, the codemod doesn't recognise the call as equivalent to the dot-notation form and skips the rename entirely.

- **Cross-scope binding assignment**: When a variable is declared in an outer scope and then assigned inside a nested function, the codemod loses track of the binding and fails to rename deprecated method calls made on that variable.

- **Namespace imports**: When code uses ESM namespace-style imports or the equivalent TypeScript require-style form and then accesses deprecated symbols via the namespace object, the codemod doesn't flag those usages for migration.

## Expected Behavior

All of the above patterns should be handled identically to the straightforward cases that already work:
- Deprecated export specifiers in re-export declarations should be renamed or removed, and specifiers whose source package has changed should be redirected to the correct package
- When merging a redirected specifier into an existing re-export from the target package, a type-only export declaration must not be used for this merge — a separate value export must be created instead, to avoid inadvertently changing a runtime symbol into a type-only one
- Computed method calls should be treated as equivalent to their dot-notation counterparts, and the output should use dot notation
- Bindings assigned in nested scopes should still be tracked for renaming by keying them to their declaring scope
- Namespace-qualified usage of deprecated APIs should receive the same TODO migration comments as directly imported usage

## Why This Matters

Projects commonly use barrel re-exports, namespace imports, and scope-separated declarations. Without these fixes, running the codemod gives a false sense of completeness while leaving broken code behind that will only surface at compile or runtime.
