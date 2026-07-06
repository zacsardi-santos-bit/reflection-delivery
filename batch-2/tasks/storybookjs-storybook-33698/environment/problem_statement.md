## Description

Storybook's TypeScript loader currently checks whether each candidate file exists by making individual filesystem calls per file. This is inefficient when processing files with many imports — each extensionless import triggers multiple separate existence checks. We should batch these into directory-level reads and cache the results.

Additionally, many TypeScript projects using modern module resolution are configured to write import paths with `.js` extensions even though the actual source files are `.ts`. The loader currently leaves these `.js`-extension imports untouched, which means it cannot resolve them to the correct TypeScript source files. We need the loader to detect when a `.js` import corresponds to an existing `.ts` source file and rewrite the import path accordingly — without triggering a deprecation warning, since this is a legitimate pattern, not an oversight.

## Expected Behavior

- Import paths that use a `.js` extension should be rewritten to `.ts` when a TypeScript source file with the same base name exists in the same directory.
- Imports that already carry a TypeScript or other non-remappable extension should be left unchanged.
- Extensionless imports should continue to be resolved and trigger a deprecation warning as before.
- Directory contents should be read once per directory and cached internally. A mechanism to clear this cache must be provided so that callers can reset state between operations.

## Why This Matters

TypeScript projects configured with modern module resolution settings write `.js` extension imports in source files even though the actual files on disk use `.ts` extensions. Without this fix, Storybook's loader cannot handle those projects correctly. The caching improvement also reduces unnecessary filesystem I/O when the same directory is referenced by multiple imports.
