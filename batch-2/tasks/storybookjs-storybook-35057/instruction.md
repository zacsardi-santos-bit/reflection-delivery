Implement a mechanism to flag certain operations or services as "internal" within a service registry system, ensuring they are hidden from discovery APIs but remain functional at runtime. Update the service definition system and related functions to support this feature, and add necessary fixtures for testing.

*   Update the service definition system:
    *   Add an optional `internal` boolean property to service definitions, query definitions, and command definitions. This property defaults to false.
    *   Ensure `internal` property is recognized by `defineService()` in `service-definition.ts`.

*   Modify `listServices()` in `service-registry.ts`:
    *   Exclude services with `internal: true` from the returned list.
    *   Ensure `queryNames` and `commandNames` in each service summary exclude operations with `internal: true`.

*   Modify `describeService(id)` in `service-registry.ts`:
    *   Exclude queries and commands with `internal: true` from the returned descriptor.
    *   Return the full descriptor for services with `internal: true` when queried by id.

*   Ensure `getService(id)` in `service-registry.ts`:
    *   Continues to return service handles for services with `internal: true`.
    *   Allows runtime calls to queries and commands on those handles to function normally.

*   Ensure `buildStaticFiles()` in `service-registry.ts`:
    *   Includes output from queries with `internal: true` and a `staticPath` function in the static snapshot build.

*   Update `code/core/src/shared/open-service/fixtures.ts`:
    *   Add `registeredCommandOverrideServiceDef` with service id `'internal-fixture/registered-command-override'`.
    *   Add `mixedVisibilityServiceDef` with service id `'internal-fixture/mixed-visibility'`.
    *   Add `hiddenServiceDef` with service id `'internal-fixture/hidden-service'`.
    *   Add `internalStaticBuildServiceDef` with service id `'internal-fixture/internal-static-build'`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.