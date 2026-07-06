I'm working on adding consistent input validation and standardized response shapes to our server's API routes.

*   The GET /api/remote-health endpoint must return HTTP 200 with body { status: 'OK', message: 'healthy' } when remote generation is enabled, and HTTP 200 with body { status: 'DISABLED', message: 'remote generation and grading are disabled' } when remote generation is disabled (remote URL is null), without invoking the remote health check in the disabled case.

*   The GET /api/results endpoint must validate the 'type' query parameter; invalid values must return HTTP 400 with an error response whose 'error' field mentions 'type'. Valid requests return HTTP 200 with body { data: [...] }.

*   The GET /api/results endpoint must coerce the 'includeProviders' query parameter to boolean true only when the value is the exact string 'true'; all other values including 'false', '1', 'yes', and omitted must resolve to false.

*   The GET /api/results/:id endpoint must return HTTP 404 with body { error: 'Result not found' } when the requested eval result does not exist.

*   The GET /api/prompts/:hash endpoint must validate that the hash path parameter is a 64-character hexadecimal string (SHA-256 hash). Invalid hashes must return HTTP 400 with an error response whose 'error' field mentions 'sha256hash'. Valid hashes return HTTP 200 with body { data: [...] }.

*   The GET /api/history endpoint must accept 'tagName' and 'tagValue' query parameters and return HTTP 200 with body { data: [...] }.

*   The GET /api/prompts endpoint must return HTTP 200 with body { data: [...] }. The GET /api/datasets endpoint must return HTTP 200 with body { data: [...] }.

*   The GET /api/results/share/check-domain endpoint must return HTTP 400 with an error response whose 'error' field mentions 'id' when the 'id' query parameter is missing or is the literal string 'undefined'. When the eval is not found it must return HTTP 404 with body { error: 'Eval not found' }. When found, it must return HTTP 200 with body { domain: string, isCloudEnabled: boolean }.

*   The POST /api/results/share endpoint must return HTTP 400 with an error response whose 'error' field mentions 'id' when the request body lacks an 'id' field. When the eval is not found it must return HTTP 404 with body { error: 'Eval not found' }. When valid it must return HTTP 200 with body { url: string }. The endpoint must not impose rate limiting.

*   The POST /api/dataset/generate endpoint must return HTTP 400 with an error mentioning 'prompts' when the prompts array is empty or contains null values, and HTTP 400 with an error mentioning 'tests' when the tests array contains null values. String prompts must be normalized to objects with 'raw' and 'label' fields (label defaults to the raw string). Object prompts without a 'label' field must have label set to the value of 'raw'; extra fields on object prompts must be preserved. Valid requests return HTTP 200 with body { results: [...] }.

*   The POST /api/telemetry endpoint must return HTTP 400 with an error mentioning 'event' when the event name is not a known telemetry event. It must return HTTP 400 with an error mentioning 'properties' when the properties object contains nested objects (property values must be strings, numbers, booleans, or arrays of strings). When telemetry recording throws an error it must return HTTP 500 with body { error: 'Failed to process telemetry request' }. Valid requests return HTTP 200 with body { success: true }.

*   The GET /health endpoint must return HTTP 200 with a body containing { status: 'OK' } and a non-empty string 'version' field.

*   The module at src/telemetry.ts must export a constant named TELEMETRY_EVENTS that is an array of valid telemetry event name strings, including at minimum 'webui_api' and 'webui_action'.

*   The module at src/telemetry.ts must export a schema named TelemetryEventSchema that validates telemetry event objects. When parsed, the schema must add a 'packageVersion' field to the output alongside the original 'event' and 'properties' fields.

*   The modules at src/types/api/server.ts and src/telemetryEvents.ts must be importable without triggering any writes to the user configuration directory.


*   Interface details: Type: Constant
Name: TELEMETRY_EVENTS
Location: src/telemetry.ts
Signature: TELEMETRY_EVENTS: readonly string[]
Description: An array of all valid telemetry event name strings. Must include at minimum 'webui_api' and 'webui_action'. Exported as a named export from the telemetry module so other modules can validate event names without duplicating the list.

Type: Object (Zod schema)
Name: TelemetryEventSchema
Location: src/telemetry.ts
Signature: TelemetryEventSchema: ZodSchema — parse({ event: string, properties?: Record<string, string | number | boolean | string[]> }) -> { event: string, packageVersion: string, properties?: Record<string, string | number | boolean | string[]> }
Description: A Zod schema exported from the telemetry module. When parsed with a valid telemetry event object, it returns the object with an additional 'packageVersion' field added. Used by the server API to validate incoming telemetry requests and by other modules that need to validate or construct telemetry payloads.

Type: File
Name: src/types/api/server.ts
Location: src/types/api/server.ts
Description: A new module containing server API DTO schemas. Must be importable without writing to or modifying the user configuration directory. This file contains the validation schemas used by the server routes for request and response DTOs.

Type: File
Name: src/telemetryEvents.ts
Location: src/telemetryEvents.ts
Description: A module containing telemetry event definitions. Must be importable without writing to or modifying the user configuration directory. Re-exported or used by src/telemetry.ts for the TELEMETRY_EVENTS and TelemetryEventSchema exports.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.