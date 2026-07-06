Implement improvements to Storybook's TypeScript file loader to enhance import path resolution and optimize filesystem operations. Modify how import paths are resolved, particularly for `.js` extensions, and introduce caching for directory reads.

*   Implement and export the `clearDirectoryCache` function in `code/core/src/bin/loader.ts`.
    *   Signature: `clearDirectoryCache(): void`
    *   Clears all cached directory listing data to ensure fresh data is read from the filesystem on subsequent operations.

*   Update the `resolveWithExtension` function in `code/core/src/bin/loader.ts`.
    *   Use `readdirSync` from `node:fs` to read directory contents instead of individual file existence checks.
    *   Cache directory contents internally and ensure `clearDirectoryCache` resets this cache.
    *   If an import path has a `.js` extension and a corresponding `.ts` file exists, return the path with the `.ts` extension. Do not emit a deprecation warning for this remapping.
    *   If an import path has a non-JS extension (e.g., `.ts`, `.mjs`, `.tsx`, `.mts`), return the original path unchanged without a deprecation warning.
    *   For extensionless import paths, attempt to resolve using supported extensions in order, emit a deprecation warning, and return the resolved path. If no match is found, return the original path.

*   Modify the `addExtensionsToRelativeImports` function in `code/core/src/bin/loader.ts`.
    *   Transform relative import/export statements by resolving their extensions using `resolveWithExtension`.
    *   Rewrite `.js` imports to `.ts` when a corresponding TypeScript file exists in the directory, without triggering a deprecation warning.
    *   Leave imports with non-JS-mapped extensions (e.g., `.ts`, `.mjs`) unchanged.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.