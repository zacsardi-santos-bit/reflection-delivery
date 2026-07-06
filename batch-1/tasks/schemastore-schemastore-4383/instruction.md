Create a JSON schema for the gematik Tiger test platform to enable editor validation and autocompletion for YAML configuration files. Register this schema in the SchemaStore to ensure it is automatically recognized by editors for files following the Tiger configuration naming convention.

*   Implement a JSON schema file at `src/schemas/json/gematik-tiger.json`:
    *   Define the root type as "object".
    *   Include a "tigerProxy" property supporting:
        *   `skipTrafficEndpointsSubscription` (boolean)
        *   `proxyPort` (integer)
        *   `trafficEndpoints` (array of strings)
        *   `keyFolders` (array of strings)
        *   `activateRbelParsingFor` (array of strings)
    *   Include a "servers" property as an object/map:
        *   Each server entry must support:
            *   `type` (string)
            *   `serverPort` (integer)
            *   `healthcheckUrl` (string)
            *   `tigerProxyConfiguration` (object with `adminPort`, `proxyPort`, and `proxyRoutes` containing `from` and `to` fields)
    *   Allow additional root-level properties, ensuring no validation errors for keys like `lib`, `additionalConfigurationFiles`, `editorExamples`, `tigerGlue`, and arbitrary booleans.

*   Register the schema in `src/api/json/catalog.json`:
    *   Add a new entry with:
        *   `"name"`: A human-readable name for the schema.
        *   `"description"`: A short description of the schema.
        *   `"fileMatch"`: An array including `"**/tiger.yml"` and `"**/tiger.yaml"`.
        *   `"url"`: Set to `"https://json.schemastore.org/gematik-tiger.json"`.

*   Ensure the CLI tool can locate the schema by the name `gematik-tiger.json`:
    *   Verify that running `node ./cli.js check --schema-name gematik-tiger.json` exits with code 0 for valid test YAML files.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.