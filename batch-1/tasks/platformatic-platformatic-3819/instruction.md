Implement the `responsesWriter` function to enhance TypeScript client code generation by including documentation comments derived from OpenAPI specifications. Ensure that response definitions with 'summary' and/or 'description' fields are properly documented in the generated TypeScript types.

*   Modify the `responsesWriter` function located in `packages/client-cli/lib/responses-writer.mjs`.
*   Inspect each response entry in the `responses` object for 'summary' and 'description' properties.
    *   If both 'summary' and 'description' are present, emit a TSDoc block comment before the TypeScript type definition:
        *   Include the summary on the first content line.
        *   Insert a blank ' *' line as a separator.
        *   Include the description on the subsequent content line.
    *   If only 'description' is present, emit a TSDoc block comment containing only the description text.
    *   If neither 'summary' nor 'description' is present, do not emit any TSDoc comment (maintain existing behavior).
*   Ensure the return value of `responsesWriter` (the union type name string, e.g., 'MyOperationResponses') remains unchanged.
*   Preserve the existing structure of the generated TypeScript type body, including property names, types, and required/optional markers. Only add the leading TSDoc comment as specified.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.