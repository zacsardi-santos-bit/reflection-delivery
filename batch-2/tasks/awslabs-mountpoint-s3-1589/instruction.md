Update the filesystem integration tests to use an asynchronous mock HTTP server library. Replace the current synchronous mock server with one that supports async operations for server startup and mock response registration.

*   Modify the `Cargo.toml` file:
    *   Replace the `httpmock = "0.7.0"` dependency with `wiremock = "0.6.2"` under `[dev-dependencies]` for the `mountpoint-s3-fs` package.

*   Implement the new mock server setup:
    *   Ensure the mock server starts asynchronously using `await` for initialization.
    *   Register mock responses asynchronously, specifying HTTP method, path, and query parameters using a fluent builder style.
    *   Retrieve the server endpoint as a complete URI string.

*   Verify test scenarios:
    *   Ensure tests for S3 throttling responses (HTTP 503) continue to pass.
    *   Ensure tests for unexpected HTTP error codes during lookup operations (HTTP 409) continue to pass.
    *   Ensure tests for unexpected HTTP error codes during read operations (HTTP 418) continue to pass.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.