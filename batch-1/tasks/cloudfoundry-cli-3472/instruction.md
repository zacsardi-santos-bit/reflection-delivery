Implement distributed tracing support in the CF CLI by adding B3-style tracing headers to HTTP requests sent to the Cloud Controller, UAA, and Routing APIs. Create connection wrapper types for each API client to automatically inject these headers. Develop utilities for managing and setting headers, and functions for generating trace IDs.

*   Implement the `GenerateUUIDTraceID` function in `util/trace/trace.go`:
    *   Return a 32-character UUID v4 string with dashes removed.

*   Implement the `GenerateRandomTraceID` function in `util/trace/trace.go`:
    *   Accept an integer `length` and return a random hex-encoded string of that length.

*   Develop the `TraceHeaders` struct in `api/shared/trace_headers.go`:
    *   Store a B3 trace ID and provide a method to set B3 trace/span headers on an HTTP request.

*   Implement the `NewTraceHeaders` function in `api/shared/trace_headers.go`:
    *   Accept a trace ID string and return a pointer to a new `TraceHeaders` initialized with it.

*   Implement the `SetHeaders` method in `api/shared/trace_headers.go`:
    *   Set the `X-B3-TraceId` header on the provided `*http.Request` to the trace ID stored in `TraceHeaders` if not already present.
    *   Set the `X-B3-SpanId` header to a non-empty randomly generated value if not already present.

*   Implement the `NewCCTraceHeaderRequest` function in `api/cloudcontroller/wrapper/trace_request.go`:
    *   Return a `*CCTraceHeaderRequest` initialized with the given trace ID.
    *   Ensure `Wrap` is called to attach an inner connection before use.

*   Implement the `CCTraceHeaderRequest` struct in `api/cloudcontroller/wrapper/trace_request.go`:
    *   Ensure the `Make` method sets `X-B3-TraceId` and `X-B3-SpanId` headers on requests before delegating to the inner connection's `Make`.

*   Implement the `NewRoutingTraceHeaderRequest` function in `api/router/wrapper/trace_request.go`:
    *   Return a `*RoutingTraceHeaderRequest` initialized with the given trace ID.
    *   Ensure `Wrap` is called to attach an inner connection before use.

*   Implement the `RoutingTraceHeaderRequest` struct in `api/router/wrapper/trace_request.go`:
    *   Ensure the `Make` method sets `X-B3-TraceId` and `X-B3-SpanId` headers on requests before delegating to the inner connection's `Make`.

*   Implement the `NewUAATraceHeaderRequest` function in `api/uaa/wrapper/trace_request.go`:
    *   Return a `*UAATraceHeaderRequest` initialized with the given trace ID.
    *   Ensure `Wrap` is called to attach an inner connection before use.

*   Implement the `UAATraceHeaderRequest` struct in `api/uaa/wrapper/trace_request.go`:
    *   Ensure the `Make` method sets `X-B3-TraceId` and `X-B3-SpanId` headers on requests before delegating to the inner connection's `Make`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.