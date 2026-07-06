Implement a utility function to convert plugins from an external bundler's API to be compatible with Vite's internal plugin system. Ensure that end-of-build callbacks are correctly invoked with the expected result object.

*   Implement the `convertEsbuildPluginToRolldownPlugin` function in `packages/vite/src/node/optimizer/pluginConverter.ts`.
    *   Accept an `esbuild.Plugin` object as input.
    *   Return a Vite/Rolldown Plugin object.
*   Ensure the returned plugin includes:
    *   An `options` hook:
        *   Call the esbuild plugin's `setup(build)` function.
        *   Pass a `build` object with an `onEnd(callback)` method for registering end-of-build callbacks.
    *   A `generateBundle` hook:
        *   Invoke all registered `onEnd` callbacks.
        *   Pass a `BuildResult`-shaped object with fields:
            *   `outputFiles` set to `undefined`.
            *   `metafile` set to `undefined`.
            *   `mangleCache` set to `undefined`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.