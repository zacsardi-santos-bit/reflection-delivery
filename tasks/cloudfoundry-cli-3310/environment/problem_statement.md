## Description

The CloudFoundry CLI makes HTTP requests to multiple backend services (Cloud Controller, Router API, UAA) but does not currently attach any distributed tracing context to those calls. This makes it very difficult to trace a single CLI operation end-to-end across backend systems when debugging or observing behavior in production.

## Expected Behavior

- When the CLI makes HTTP requests to any of the backend services, it should attach B3 distributed tracing headers (a trace ID and a span ID) to the outgoing requests.
- The trace ID should be a value provided externally (e.g. derived from a user session or command invocation), and the span ID should be randomly generated per request.
- If a request already carries B3 trace headers, the injector should leave them unchanged rather than overwriting them.
- Utility functions should be available to generate random trace identifiers of fixed lengths (e.g. a 32-character UUID-style ID and a variable-length random ID).
- The header injection should be implemented as connection middleware/wrappers so it can be cleanly added to each API client.

## Why This Matters

Without trace headers, it is impossible to correlate a CLI-initiated API call with log entries in the target backend services. Injecting B3 trace context enables operators and developers to trace requests through distributed systems using standard observability tooling.
