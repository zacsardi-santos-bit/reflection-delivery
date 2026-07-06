Develop a Sentry SDK package for Google Cloud serverless functions to facilitate automatic error capturing and tracing. Implement wrappers for HTTP, background event, and Cloud Event function handlers, and integrate automatic tracing for outgoing Google Cloud API calls.

* Implement `wrapCloudEventFunction` in `packages/google-cloud/src/gcpfunction/cloud_events.ts`:
    * Wrap both callback-based and non-callback Cloud Event handlers, returning a callback-style handler.
    * Create a span named after the event's type with specific attributes and end it after execution.
    * Call `flush` with a 2000ms timeout on success and capture exceptions using `captureException`.
    * Set the 'gcp.function.context' context on the current scope with the cloud event context object.

* Implement `wrapEventFunction` in `packages/google-cloud/src/gcpfunction/events.ts`:
    * Wrap synchronous, Promise-returning, and callback-based background Event function handlers.
    * Create a span named after `context.eventType` with specific attributes and end it after execution.
    * Call `flush` with a 2000ms timeout on success and capture exceptions, marking them as unhandled.
    * Set the 'gcp.function.context' context on the current scope with the event context object.

* Implement `wrapHttpFunction` in `packages/google-cloud/src/gcpfunction/http.ts`:
    * Wrap HTTP function handlers, creating a span named 'METHOD /path' with specific attributes.
    * Accept an optional `flushTimeout` option, defaulting to 2000, and call `flush` after the response ends.
    * Store the request object in SDK processing metadata and ensure no exceptions are thrown on flush failure.

* Implement `init` in `packages/google-cloud/src/sdk.ts`:
    * Initialize the SDK with metadata including `_metadata.sdk.name` and `_metadata.sdk.packages`.
    * Include the `RequestData` integration in the default integrations.

* Implement `googleCloudGrpcIntegration` in `packages/google-cloud/src/integrations/google-cloud-grpc.ts`:
    * Intercept unary gRPC calls and create inactive spans with specific attributes and conditions.

* Implement `googleCloudHttpIntegration` in `packages/google-cloud/src/integrations/google-cloud-http.ts`:
    * Intercept HTTP calls to Google Cloud REST APIs and create inactive spans with specific attributes and conditions.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.