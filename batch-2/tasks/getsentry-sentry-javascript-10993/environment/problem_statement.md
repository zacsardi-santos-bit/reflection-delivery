## Description

We need a dedicated Sentry SDK package for Google Cloud serverless workloads. Developers running applications on Google Cloud Functions currently have no idiomatic way to integrate Sentry monitoring — they cannot easily capture unhandled exceptions from their serverless handlers, record performance traces per function invocation, or automatically trace outgoing calls to Google Cloud services.

## Expected Behavior

- Developers should be able to wrap their Google Cloud HTTP function handlers to automatically capture exceptions and record a performance span for each invocation. The span should be named after the HTTP method and path, and any pending events should be sent to Sentry after each response completes. If sending those events fails, the error should be swallowed rather than surfaced to the caller.
- Developers should be able to wrap background event function handlers (both synchronous and asynchronous, with or without callbacks) to capture errors and record spans named after the event type. Exceptions thrown during these handlers should be reported to Sentry and marked as unhandled.
- Developers should be able to wrap Cloud Event function handlers similarly, with errors automatically captured and marked as unhandled, and spans recorded per invocation.
- The SDK should automatically trace outgoing Google Cloud API calls over HTTP (e.g. BigQuery queries) and gRPC (e.g. Pub/Sub publishes) by instrumenting the relevant client libraries.
- The SDK initialization should register itself with correct metadata so Sentry can identify events as coming from this SDK.
- The package should export a consistent set of symbols compatible with the existing Node SDK surface.

## Why This Matters

Without this package, Google Cloud Function users must manually instrument every function handler for error capture and tracing, and have no support at all for automatic tracing of Google Cloud API calls. A dedicated SDK removes this friction and brings Google Cloud Functions to parity with other serverless platforms already supported.
