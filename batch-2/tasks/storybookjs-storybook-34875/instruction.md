Implement a server-side module to manage a global service registry, enabling services to register, discover, and interact with each other. Ensure static file generation utilizes this registry, normalizing paths and handling errors appropriately.

*   Implement `registerService` in `code/core/src/shared/open-service/server.ts`:
    *   Accept a service definition and optional handler implementations.
    *   Register the service in a global registry and return a service instance with `queries` and `commands`.
    *   Throw a Storybook error if the same service ID is registered twice.

*   Implement `getService` in `code/core/src/shared/open-service/server.ts`:
    *   Resolve a registered service by its ID, returning the original service instance.
    *   Reject with a Storybook error if the service ID is not found.

*   Implement `getRegisteredServices` in `code/core/src/shared/open-service/server.ts`:
    *   Return a synchronous array of all registered service instances.

*   Implement `listServices` in `code/core/src/shared/open-service/server.ts`:
    *   Return a Promise resolving to an array of service summary objects with `id`, `description`, `queryNames`, and `commandNames`.

*   Implement `describeService` in `code/core/src/shared/open-service/server.ts`:
    *   Return a Promise resolving to a detailed descriptor of a service, including `queries` and `commands`.

*   Implement `clearRegistry` in `code/core/src/shared/open-service/server.ts`:
    *   Remove all services from the global registry.

*   Implement `buildStaticFiles` in `code/core/src/shared/open-service/server.ts`:
    *   Operate on the global registry to build static snapshot files.
    *   Normalize file paths and reject paths with `..` segments with a Storybook error.
    *   Deep-merge state objects for queries resolving to the same path.

*   Implement `writeOpenServiceStaticFiles` in `code/core/src/shared/open-service/server.ts`:
    *   Call `buildStaticFiles()` and write JSON files to `{outputDir}/services/{normalizedPath}`.
    *   Ensure files are pretty-printed with 2-space indentation and create necessary directories.

*   Ensure all error messages are clear, structured, and use the Storybook error format with appropriate codes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.