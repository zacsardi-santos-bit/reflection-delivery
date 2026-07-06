Implement the necessary changes to the Gen 1 to Gen 2 Amplify migration code generator to dynamically handle environment names and GraphQL schemas. Ensure that the generated code is adaptable to different environments and that the migration process is streamlined by automatically integrating existing schemas.

*   Update the `BackendSynthesizer` class in `packages/amplify-gen2-codegen/src/backend/synthesizer.ts`:
    *   Modify the `render` method to read `AMPLIFY_GEN_1_ENV_NAME` dynamically from the process environment.
    *   Implement a CI validation block that throws an error with the message 'AMPLIFY_GEN_1_ENV_NAME is required in CI environment' if `ci.isCI` is true and `AMPLIFY_GEN_1_ENV_NAME` is not set.
    *   Ensure a fallback to 'sandbox' for `AMPLIFY_GEN_1_ENV_NAME` when not in a CI environment.

*   Modify the `generateDataSource` function in `packages/amplify-gen2-codegen/src/data/source_builder.ts`:
    *   Include a `schema: string` field in the `DataDefinition` interface.
    *   Accept an optional `schema` property and include it in the output as a template literal variable.
    *   Use shorthand notation for the `schema` property in the `defineData` call.
    *   Remove the `schemaPlaceholderComment` export and ensure the old placeholder is not present in the output.

*   Update the `AmplifyDevDependencies` type in `packages/amplify-gen2-codegen/src/npm_package/renderer.ts`:
    *   Add 'ci-info' as a string key.
    *   Ensure `patchNpmPackageJson` handles 'ci-info' as a dev dependency.

*   Modify the `renderStorage` function in `packages/amplify-gen2-codegen/src/storage/source_builder.ts`:
    *   Generate the `name` property as a template literal using `AMPLIFY_GEN_1_ENV_NAME` when `storageIdentifier` is provided.

*   Implement the `DataDefinitionFetcher` class in `packages/amplify-migration/src/data_definition_fetcher.ts`:
    *   Add a `getSchema()` method that returns a `Promise<string>`.
    *   Locate the AppSync API resource name using `stateManager.getMeta()`.
    *   Find the project root with `pathManager.findProjectRoot()` and check for schema files under `amplify/backend/api/{apiName}/schema/`.
    *   Merge contents of all `.graphql` files if the schema directory exists, or fall back to reading `schema.graphql`.
    *   Throw an error with the message 'No GraphQL schema found in the project' if no schema files are found.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.