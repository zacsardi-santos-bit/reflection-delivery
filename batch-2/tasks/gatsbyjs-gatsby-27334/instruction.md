Implement a utility function to validate plugin option schemas in Gatsby plugins. This function should allow plugin authors to test their schema definitions against a set of options to ensure correct validation without initiating a full build process.

*   Implement the `testPluginOptionsSchema` function with the following signature:
    *   Location: `packages/gatsby-plugin-utils/src/test-plugin-options-schema.ts`
    *   Signature: `testPluginOptionsSchema(pluginSchemaFunction: ({ Joi }: { Joi: any }) => ObjectSchema, pluginOptions: object) -> { isValid: boolean, errors: Array<string> }`
    *   Description: Validates a partial set of plugin options against a Joi-based plugin schema and returns a result indicating validity and error messages.

*   Ensure the function:
    *   Accepts a plugin schema factory function and a partial options object.
    *   Returns an object with `isValid` (boolean) and `errors` (array of strings).
    *   Returns `{ isValid: true, errors: [] }` when all option values pass validation.
    *   Returns `{ isValid: false, errors: [...] }` with error messages for failing fields.
    *   Validates only fields present in the test options object.
    *   Produces no errors for fields not included in the test options, even if required by the schema.

*   Format error messages as follows:
    *   Scalar field type mismatches: `'"fieldName" must be a <type>'`
    *   Required fields passed as undefined: `'"fieldName" is required'`
    *   Array fields with invalid items: Concatenate all item errors in a single entry, e.g., `'"arrayField" "[0]" must be a string. "[1]" must be a string. "[2]" must be a string'`.

*   Export and re-export requirements:
    *   Export `testPluginOptionsSchema` from `packages/gatsby-plugin-utils/src/test-plugin-options-schema.ts`.
    *   Re-export `testPluginOptionsSchema` from `packages/gatsby-plugin-utils/src/index.ts` using `export * from "./test-plugin-options-schema"`.
    *   Export `ObjectSchema` type from `packages/gatsby-plugin-utils/src/utils/plugin-options-schema-joi-type.ts`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.