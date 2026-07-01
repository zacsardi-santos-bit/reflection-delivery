Implement support for connecting to on-premises ABAP SAP systems in the OData service inquirer package. Update the connection validation API to use structured options objects instead of positional boolean parameters for clarity and extensibility.

*   Update the `ConnectionValidator` class:
    *   Move it to `packages/odata-service-inquirer/src/prompts/connectionValidator.ts`.
    *   Modify `validateUrl` to accept an options object with fields: `ignoreCertError`, `forceReValidation`, `isSystem`, and `odataVersion`.
    *   Modify `validateAuth` to accept an options object with fields: `isSystem`, `sapClient`, and `ignoreCertError`.
    *   Use `createForAbap` for system connections and populate `catalogs` with `V2CatalogService` and `V4CatalogService`.
    *   Return 'URL not found' for 404 errors in `validateAuth`, with validity state `{ urlFormat: true, reachable: false }`.
    *   For non-system URL validation, use `axiosExtension.create` and `ServiceProvider.service(path)`.

*   Implement the `getAbapOnPremQuestions` function:
    *   Return questions for `systemUrl`, `sapClient`, `abapSystemUsername`, `abapSystemPassword`, `userSystemName`, `serviceSelection`, and `cliServicePromptName`.
    *   Exclude `userSystemName` if `options.userSystemName.exclude` is true.
    *   Support type-ahead autocomplete for `serviceSelection` if `options.serviceSelection.useAutoComplete` is true.

*   Implement the `getUserSystemNameQuestion` function:
    *   Return a question for `userSystemName` with a default function that suggests a name based on `systemUrl`.
    *   Validate uniqueness using `validateSystemName` and update `PromptState.odataService.connectedSystem.backendSystem` on success.

*   Implement the `getNewSystemQuestions` function:
    *   Return a questions array starting with a `newSystemType` list question followed by questions from `getAbapOnPremQuestions`.

*   Implement the `suggestSystemName` function:
    *   Suggest a system name based on `systemUrl` and optional `client`, avoiding conflicts with stored names.

*   Ensure the main prompt list includes SAP system questions with specified properties.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.