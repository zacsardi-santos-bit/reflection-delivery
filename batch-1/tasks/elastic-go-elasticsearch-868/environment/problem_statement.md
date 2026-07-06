## Description

Our integration tests currently require a manually running Elasticsearch server at a default address before they can execute. This makes them fragile and difficult to run in CI or other automated environments, because any developer or pipeline that wants to run them first has to stand up and configure an external Elasticsearch service.

We want to refactor the integration tests so they automatically spin up a fresh, isolated Elasticsearch instance using container technology. Each test run should manage its own server lifecycle — starting the server before the test and tearing it down afterward — without any manual setup.

As part of this effort, the integration tests across multiple packages are being consolidated into a single end-to-end testing package within a dedicated testing sub-module. The helper code for creating and managing containerized Elasticsearch instances and the restructured test files have already been written. However, the Go module configuration for this sub-module does not yet exist, so the tests cannot be compiled or run.

## Expected Behavior

- Running integration tests should no longer require an externally running Elasticsearch server.
- The tests should automatically start a containerized Elasticsearch instance using the same version as the client, with an option to override the version via an environment variable.
- Connection details (address, credentials, TLS certificate) should be derived from the container at runtime.
- The container should be properly cleaned up when tests finish.

## Why This Matters

Without a proper module configuration for the test sub-module, the entire integration test suite fails to compile. The module must be set up with the correct name and with the container orchestration library declared as a dependency so the tests can be built and run.
