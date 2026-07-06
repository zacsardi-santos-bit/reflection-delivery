Implement a built-in filter mechanism for Vite plugin hooks to allow plugin authors to specify file IDs or code patterns that a hook should apply to. This will ensure that handlers are only called for matching modules, reducing boilerplate and improving performance.

*   Create a new module at `packages/vite/src/node/plugins/pluginFilter.ts` with the following:
    *   Export constants `FALLBACK_TRUE` (value 1) and `FALLBACK_FALSE` (value 0).
    *   Define type aliases `StringFilter`, `PluginFilterWithFallback`, and `TransformHookFilter`.
    *   Implement functions `createIdFilter`, `createCodeFilter`, and `createFilterForTransform`.

*   Implement `createIdFilter(filter: StringFilter | undefined): PluginFilterWithFallback | undefined`:
    *   Return `undefined` if `filter` is `undefined`.
    *   Return a function that:
        *   Returns `true` if the ID matches an include pattern.
        *   Returns `false` if the ID matches an exclude pattern.
        *   Returns `FALLBACK_FALSE` if no include patterns match.
        *   Returns `FALLBACK_TRUE` if no exclude patterns match.
    *   Handle string patterns as glob patterns relative to `process.cwd()`.
    *   Exclude IDs starting with `\0` from matching string/glob patterns.

*   Implement `createCodeFilter(filter: StringFilter | undefined): PluginFilterWithFallback | undefined`:
    *   Return `undefined` if `filter` is `undefined`.
    *   Return a function that:
        *   Returns `true` if the code matches an include pattern.
        *   Returns `false` if the code matches an exclude pattern.
        *   Returns `FALLBACK_FALSE` if no include patterns match.
        *   Returns `FALLBACK_TRUE` if no exclude patterns match.
    *   Use substring matching for string patterns and regex testing for RegExp patterns.

*   Implement `createFilterForTransform(idFilter: StringFilter | undefined, codeFilter: StringFilter | undefined): TransformHookFilter | undefined`:
    *   Return `undefined` if both filters are `undefined`.
    *   Return a function `(id: string, code: string) => boolean` that:
        *   Returns the result of the ID filter if it is a boolean.
        *   Returns the result of the code filter if it is a boolean.
        *   Combines fallback values using AND logic.

*   Update the `Plugin` interface in `packages/vite/src/node/plugin.ts`:
    *   Allow `resolveId`, `load`, and `transform` hooks to accept an optional `filter` property:
        *   `resolveId`: `filter?: { id?: StringFilter }`
        *   `load`: `filter?: { id?: StringFilter }`
        *   `transform`: `filter?: { id?: StringFilter; code?: StringFilter }`

*   Ensure Vite's plugin container skips calling handlers for non-matching IDs or code patterns:
    *   Apply this behavior in both the development server and production build pipeline.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.