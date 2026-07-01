Update the Nx rollup plugin to correctly load rollup configuration files using the public API provided by the current rollup version. Ensure the plugin can automatically discover and configure build targets for projects using rollup, regardless of the number of outputs defined in the configuration.

*   Modify the `createNodes` plugin implementation in `packages/rollup/src/plugins/plugin.ts`:
    *   Import the config-loading utility as a named export called `loadConfigFile` from the module path `rollup/loadConfigFile`.
    *   Use `loadConfigFile` to load rollup configuration files instead of directly requiring or importing them.
    *   Ensure the function returns an object with an `options` array where each element contains an `output` field.
*   Ensure the `createNodes` plugin correctly produces project nodes:
    *   For rollup configs defining a single output entry (e.g., a root project with one CJS output).
    *   For rollup configs defining multiple output entries (e.g., a non-root project with both CJS and ES module outputs).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.