Implement a unified server-side module to maintain a global service registry for registering and accessing services. Ensure services can be retrieved by their string identifier during both runtime and static builds, and handle errors for duplicate registrations and missing services or handlers.

*   Implement `registerService` in `code/core/src/shared/open-service/server.ts`:
    *   Register a service definition into a global registry.
    *   Return the service instance.
    *   Accept optional handler overrides for commands and queries.
    *   Throw an error with `fromStorybook: true`, `code: 6`, and message 'A service with id "<id>" is already registered.' if the service ID is already registered.

*   Implement `clearRegistry` in `code/core/src/shared/open-service/server.ts`:
    *   Remove all registered services from the global registry.

*   Implement `getService` in `code/core/src/shared/open-service/server.ts`:
    *   Accept a service ID string.
    *   Return a Promise resolving to the registered service instance.
    *   Reject with an error with `fromStorybook: true`, `code: 7`, and message 'No registered service with id "<id>" exists in this environment.' if the ID is not registered.

*   Implement `getRegisteredServices` in `code/core/src/shared/open-service/server.ts`:
    *   Synchronously return an array of all currently registered service instances.

*   Implement `listServices` in `code/core/src/shared/open-service/server.ts`:
    *   Return a Promise resolving to an array of summary objects for all registered services, including `id`, `description`, `queryNames`, and `commandNames`.

*   Implement `describeService` in `code/core/src/shared/open-service/server.ts`:
    *   Return a Promise resolving to a full descriptor object for a given service ID.
    *   Include `id`, `description`, `queries`, and `commands` with schema references.

*   Ensure service handlers can look up other registered services by ID during runtime and static build preloads.

*   Implement `buildStaticFiles` in `code/core/src/shared/open-service/server.ts`:
    *   Operate on the global service registry without arguments.
    *   Return a Promise resolving to a Record mapping normalized logical path strings to state objects.
    *   Normalize custom static paths and reject paths with '..' segments with an error with `fromStorybook: true`, `code: 10`, and a specific message.
    *   Run preload tasks in parallel.

*   Implement `writeOpenServiceStaticFiles` in `code/core/src/shared/open-service/server.ts`:
    *   Accept an output directory path.
    *   Write static snapshot files to `<outputDir>/services/<normalized-path>`.
    *   Serialize file contents as pretty-printed JSON with 2-space indentation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.