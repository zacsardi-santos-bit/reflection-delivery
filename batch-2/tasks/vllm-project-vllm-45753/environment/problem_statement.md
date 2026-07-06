## Description

The Rust frontend for the vLLM serving system does not support configuring cross-origin resource sharing (CORS) policies. When browser-based clients running on a different origin try to access the API, there is no way to configure which origins, HTTP methods, or headers are permitted. Previously, attempting to pass CORS credential settings via the command line was rejected as unsupported, and passing them via JSON configuration also produced errors.

This makes it impossible for operators to allow or restrict web clients on specific origins from accessing the API, which is essential for real-world deployments where the UI is served from a different domain than the API server.

## Expected Behavior

- The server should accept CORS configuration flags for permitted origins, methods, headers, and credential handling.
- The defaults should be permissive (all origins, all methods, all headers, credentials off), matching the behavior of the existing Python-based server.
- When an explicit list of allowed origins is provided, the server should reflect the matched origin back to the browser and set the appropriate cache-varying response header; unrecognized origins should be silently rejected.
- When credentials are enabled alongside wildcard origins, the server must reflect the actual request origin rather than the literal wildcard, so browsers accept the response.
- Browser preflight requests (OPTIONS) must bypass API key authentication so clients can probe allowed methods and headers before sending the real request.
- Unauthorized requests must not leak CORS headers.
- The wildcard method list should expand to an explicit set of standard HTTP methods in preflight responses, rather than passing through the literal wildcard.

## Why This Matters

Without CORS support in the Rust frontend, operators running browser-based applications against the API must fall back to the Python server or add a reverse proxy to inject CORS headers manually. Adding first-class CORS configuration makes the Rust frontend fully production-ready for browser clients.
