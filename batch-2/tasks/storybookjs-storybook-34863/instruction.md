Implement a runtime instance registry for Storybook that writes a JSON file to disk whenever a Storybook server starts. This will allow external tools to discover running instances and determine if an AI integration addon is available. Ensure the system supports cleaning up the record when the instance stops and provide a higher-level function to manage the full lifecycle.

*   Implement `getMcpMetadataFromMainConfig` in `code/core/src/core-server/utils/runtime-instance-registry.ts`.
    *   Return `{ status: 'not-installed' }` if `@storybook/addon-mcp` is absent from the `addons` array.
    *   Return `{ status: 'ready', endpoint: '/mcp' }` if `@storybook/addon-mcp` is a plain string in the `addons` array.
    *   Return `{ status: 'ready', endpoint: <value> }` if `@storybook/addon-mcp` is an object with `name` and `options.endpoint`.

*   Implement `createRuntimeInstanceRecord` in `code/core/src/core-server/utils/runtime-instance-registry.ts`.
    *   Return a record with `schemaVersion` set to 1.
    *   Derive the `url` field by extracting the origin from the `address` parameter, removing query parameters.
    *   Resolve the `cwd` parameter to an absolute path.
    *   Set `startedAt` and `updatedAt` to ISO 8601 strings from the `now` parameter.
    *   Default `mcp` to `{ status: 'not-installed' }` if no `mcp` option is provided.
    *   Use the provided `mcp` value in `options` if supplied, including `status` and `endpoint`.

*   Implement `writeRuntimeInstanceRecord` in `code/core/src/core-server/utils/runtime-instance-registry.ts`.
    *   Write the record as JSON to a file named `<instanceId>.json` in `registryDir`.
    *   Ensure the operation is atomic, leaving only the final `.json` file in the directory.
    *   Return a Promise that resolves to the file path: `join(registryDir, `${record.instanceId}.json`)`.
    *   Ensure parsing the file yields an object equal to the original record.

*   Implement `writeStorybookRuntimeInstanceRecord` in `code/core/src/core-server/utils/runtime-instance-registry.ts`.
    *   Return a Promise that resolves to an object with `recordPath` and an async `cleanup()` function.
    *   Ensure the file at `recordPath` exists on disk after resolution.
    *   Ensure the file is deleted from disk after calling `cleanup()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.