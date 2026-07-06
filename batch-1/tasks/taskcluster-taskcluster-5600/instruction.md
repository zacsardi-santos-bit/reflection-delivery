Implement a utility module to validate task payloads against worker type schemas before submission in the Taskcluster UI. Ensure the module fetches schemas at runtime and formats validation errors into human-readable messages.

*   Create the module at `ui/src/utils/validateTaskPayloadSchemas.js`.
*   Export a default async function `validate` with the signature:
    *   `validate(value: any, service?: string, schema?: string) -> Promise<Array<string>>`
    *   Fetch schemas using `window.fetch` during execution.
    *   Return a Promise resolving to an array of error strings, or an empty array if validation passes.
    *   Return an empty array when the payload is empty or meets all schema constraints.

*   Export a named function `formatErrorDetails` with the signature:
    *   `formatErrorDetails(error: { message: string, keyword?: string, instancePath?: string, params?: { additionalProperty?: string } }) -> string`
    *   Return the message string unchanged if the error object has only a `message` field.
    *   If `keyword` is 'type' and `instancePath` is present, return a formatted string: `"<message> '<instancePath>'"`.
    *   If `keyword` is 'additionalProperties' and `params.additionalProperty` is present, return a formatted string: `"<message> '<additionalProperty>'"`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.