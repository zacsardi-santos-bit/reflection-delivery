Implement an "update mode" in the Platformatic code generator to allow updating existing projects without overwriting custom files. Add methods and utilities to load existing project data and manage environment variables effectively.

*   Update the configuration:
    *   Modify `BaseGenerator.setConfig()` to include an `isUpdating` field in the configuration object, defaulting to `false`.
    *   Ensure this field is present in all generator types, including the DB generator.

*   Add methods for project data management:
    *   Implement `loadFromDir(serviceName, runtimeDirectory)` in `BaseGenerator` to:
        *   Read the service configuration from `<runtimeDirectory>/services/<serviceName>/platformatic.json`.
        *   Extract the template using `getServiceTemplateFromSchemaUrl` on the `$schema` value.
        *   Flatten nested `plugins.packages[n].options` objects to dot-notation paths.
        *   Resolve environment variable placeholders from the runtime `.env` file.
        *   Return an object structured as: `{ name: serviceName, template: string, fields: [], plugins: [{ name: string, options: [{ path: string, type: 'string', value: string, name: string }] }] }`.

*   Implement "update mode":
    *   When `isUpdating` is `true`, ensure `BaseGenerator.prepare()` generates only the `platformatic.json` config file at the root path (`''`).
    *   The `bg.files` array must contain exactly one file.
    *   Include `plugins.packages` in the config file with options formatted as `{VAR_NAME}` placeholders.
    *   Populate `bg.config.dependencies` with resolved package versions.

*   Add file handling method:
    *   Implement `loadFile({ path, file })` in `FileGenerator` to:
        *   Read the file from `<targetDirectory>/<path>/<file>`.
        *   Add the result to `fg.files`.
        *   Return a file object with `{ path: string, file: string, contents: string, options: {} }`.

*   Develop utility functions:
    *   Export `flattenObject(obj)` from `packages/generators/lib/utils.js` to flatten nested objects into dot-notation keys.
    *   Export `getServiceTemplateFromSchemaUrl(schemaUrl)` to map schema URLs to `@platformatic/<type>` package names.
    *   Export `envStringToObject(str)` to parse `.env` strings into key-value objects, ignoring comments and empty lines.
    *   Update `envObjectToString` to use OS-native line endings for joining key-value pairs.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.