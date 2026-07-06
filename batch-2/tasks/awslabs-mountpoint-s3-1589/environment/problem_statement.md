## Description

The filesystem integration tests that simulate S3 API responses currently rely on a synchronous mock HTTP server library. This creates friction with the rest of the async test infrastructure: the server must be started synchronously, and mock responses must be registered synchronously, while all actual S3 client operations are async. We should migrate to a newer mock HTTP server library that is designed for async use from the ground up.

## Expected Behavior

- The mock HTTP server should start asynchronously
- Mock response registration (specifying which HTTP method, path, and query parameters to match, and what response to return) should also be performed asynchronously
- The server endpoint should be retrievable as a complete URI string
- All existing test scenarios — throttling responses (HTTP 503), unexpected error codes during lookup (HTTP 409), and unexpected error codes during reads (HTTP 418) — should continue to pass

## Why This Matters

The tests that verify correct error propagation from S3 through the filesystem layer are critical for ensuring reliability. Using an async-compatible mock server makes the test infrastructure consistent with the async nature of the S3 client, reduces potential for subtle test ordering issues, and aligns with the project's async-first approach.
