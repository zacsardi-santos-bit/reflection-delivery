Implement distributed tracing in the CloudFoundry CLI by adding B3 trace headers to outgoing HTTP requests. Create middleware wrappers for each API client to inject these headers, ensuring existing headers are not overwritten. Develop utility functions for generating trace identifiers.

*   Implement a `NewTraceHeaders` constructor in the `api/shared` package:
    *   Accepts a `traceID` string and returns a `TraceHeaders` value.
    *   Provides a `SetHeaders` method to set `X-B3-TraceId` and `X-B3-SpanId` headers on HTTP requests.
    *   Ensure `SetHeaders` does not overwrite existing `X-B3-TraceId` or `X-B3-SpanId` headers.

*   Develop a `NewCCTraceHeaderRequest` function in the `api/cloudcontroller/wrapper` package:
    *   Accepts a `traceHeader` string and returns a wrapper builder with a `Wrap` method.
    *   `Wrap` method should accept a `cloudcontroller.Connection` and return a `cloudcontroller.Connection`.
    *   Ensure the `Make` method sets `X-B3-TraceId` and `X-B3-SpanId` headers, then delegates to the inner connection.

*   Create a `NewRoutingTraceHeaderRequest` function in the `api/router/wrapper` package:
    *   Similar to `NewCCTraceHeaderRequest`, but for wrapping a `router.Connection`.

*   Implement a `NewUAATraceHeaderRequest` function in the `api/uaa/wrapper` package:
    *   Similar to `NewCCTraceHeaderRequest`, but for wrapping a `uaa.Connection`.

*   Provide utility functions in the `util/trace` package:
    *   `GenerateUUIDTraceID`: Returns a 32-character randomly generated string.
    *   `GenerateRandomTraceID`: Accepts an integer length and returns a string of that length.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.