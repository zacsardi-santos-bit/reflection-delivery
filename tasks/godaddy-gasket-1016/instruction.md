Implement changes to the Gasket framework to ensure dynamic plugins can register CLI commands and are fully initialized. Move the command registration to a later lifecycle phase and ensure dynamic plugins complete all initialization phases.

*   Update the `configure` lifecycle handler:
    *   Return a config object with a 'command' property set to `process.argv[2]` when `process.argv[1]` matches a gasket file pattern and `process.argv[2]` is present.
    *   Merge command-specific overrides from the 'commands' object into the top level of the returned config and exclude the 'commands' property from the result.
    *   Ensure it does not call `gasket.execSync`.

*   Implement a `prepare` lifecycle handler in `packages/gasket-plugin-command/lib/prepare.js`:
    *   Export as a plain function.
    *   Skip execution if `config.command` is not set.
    *   Call `gasket.execSync('commands')` and `gasketBin.addCommand` with command details if `config.command` is set.

*   Ensure `gasket-plugin-command` plugin hooks include: `create`, `configure`, `prepare`, `commands`, `ready`, and `metadata`.
    *   Set the `commands` lifecycle metadata entry's parent to 'prepare'.

*   Modify the `prepare` hook in `gasket-plugin-dynamic-plugins`:
    *   Export as an object with a `handler` async function property.
    *   Filter out falsy values from `config.dynamicPlugins`.
    *   Resolve relative plugin paths against `gasket.config.root`.
    *   Push imported plugins into `config.plugins`, register them, and execute lifecycle phases in order: `init`, `configure`, `prepare`.
    *   Call `gasket.trace` with 'deduped' message for non-dynamic plugins during lifecycle calls.

*   Ensure dynamic plugins are fully initialized through all three setup phases.
*   Resolve relative paths for dynamic plugins using the application root directory.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.