## Description

The MLflow tracing codebase has several organizational issues that make it harder to use and maintain. Span-related classes are buried in a deep internal subpackage rather than being part of the main entities package where users would naturally look for them. Similarly, tracing constants are nested in an internal types subpackage when they should live at the top level of the tracing module. This scatters related things across confusing paths and forces all dependent code to import from non-obvious locations.

Additionally, the trace data deserialization is incomplete. When a trace data object is converted to a dictionary and back, the request and response fields are lost — only the spans are reconstructed. This means retrieving a persisted trace and re-hydrating it from storage does not produce a faithful copy of the original object.

Finally, the client method for retrieving a trace doesn't return a fully usable object — callers currently have no way to inspect the trace data (request, response, or individual span details such as inputs, outputs, timing, and status) from what the method returns.

## Expected Behavior

- Span-related classes (including the no-op variant used when span creation fails) should be importable directly from the main entities package
- Tracing constants should be importable from a top-level module within the tracing package, without going through an internal types subpackage
- Converting trace data to a dictionary and back should produce an identical result, including the request and response fields
- Retrieving a trace through the client API should return a complete object that includes all trace metadata and all span data (inputs, outputs, timing, status, and associated request ID)

## Why This Matters

These issues make the public API harder to navigate, cause code spread across the codebase to import from fragile internal paths, and prevent users from fully inspecting persisted trace data retrieved from the tracking server.
