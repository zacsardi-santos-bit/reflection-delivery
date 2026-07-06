Implement a new instrumentation module for Azure Functions in Node.js to enable New Relic observability. Ensure the module automatically wraps HTTP handler registrations, records transactions, and captures necessary attributes and metrics.

*   Create a new module at `lib/instrumentation/@azure/functions.js`:
    *   Export a default `initialize` function.
    *   Expose `addAttributes`, `buildCloudResourceId`, and `mapTriggerType` via the `internals` property.

*   Implement the `initialize` function:
    *   Validate the presence and format of `WEBSITE_OWNER_NAME`, `WEBSITE_RESOURCE_GROUP`, and `WEBSITE_SITE_NAME` environment variables.
    *   Log a structured warning using `logger.warn` if validation fails, detailing expected and found values, and return without wrapping.
    *   Wrap the following methods on `moduleExports.app`: `http`, `get`, `put`, `post`, `patch`, `deleteRequest`.
    *   Ensure wrapped methods are detected by the shim.

*   Implement `addAttributes`:
    *   Set attributes on the transaction's trace attributes: `faas.invocation_id`, `faas.name`, `faas.trigger`, and `cloud.resource_id`.

*   Implement `buildCloudResourceId`:
    *   Construct a cloud resource ID string using environment variables and `functionContext`.

*   Implement `mapTriggerType`:
    *   Map trigger types to categories: `http`, `timer`, `datasource`, `pubsub`, or `other`.

*   Ensure each wrapped HTTP handler:
    *   Creates a web transaction named `WebTransaction/AzureFunction/{functionName}`.
    *   Records unscoped metrics: `HttpDispatcher`, `WebTransaction`, `WebTransaction/AzureFunction/{functionName}`, `WebTransactionTotalTime`, `WebTransactionTotalTime/AzureFunction/{functionName}`, `Apdex`, `Apdex/AzureFunction/{functionName}`.
    *   Sets span event attributes: `faas.invocation_id`, `faas.name`, `faas.trigger`, `cloud.resource_id`, `request.method`, `request.uri`, `http.statusCode`.
    *   Marks the first invocation as a cold start with `faas.coldStart = true`.

*   Handle distributed tracing:
    *   Extract and propagate context from `traceparent` and `tracestate` headers.
    *   Calculate queue time from `x-request-start` header and store it on the transaction.

*   Support direct handler function registration:
    *   Use the handler function directly if passed as the second argument.

*   Extract and store the port from the request URL on the transaction.

*   Ensure the agent logger utility supports structured data:
    *   Include a `data` field in log entries when the first argument is an object with a `data` key.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.