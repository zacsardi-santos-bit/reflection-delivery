## Description

The end-to-end integration tests for the service mesh proxy are failing because they rely on outdated configuration formats and APIs that are no longer compatible with the current versions of the Envoy proxy and its control plane library.

Specifically, the tests use a deprecated style for specifying filter plugin configurations — the old untyped format is no longer accepted by Envoy. The tests must be updated to use the typed configuration format, which includes a fully-qualified protobuf type annotation.

Additionally, the discovery server in the test driver is being initialized without a required context argument, which causes a compilation failure against the updated control plane library. The snapshot cache API has also changed and now requires resources to be set via map-style access rather than named struct fields.

There are also several housekeeping issues: a deprecated Envoy command-line flag is being passed that causes startup warnings or errors, large blocks of node metadata and statistics configuration are duplicated as inline constants across multiple test files rather than being maintained in shared external template files, and a canonical service label in test assertions is incorrect.

## Expected Behavior

- The test infrastructure driver must be updated to be compatible with the current control plane library API, including context-aware server initialization and the new snapshot resource assignment pattern.
- All Envoy filter configurations in test templates must use the typed configuration format with proper protobuf type annotations.
- Wasm filter configurations must be wrapped in the appropriate typed struct wrapper.
- The deprecated Envoy startup flag must be removed.
- Large metadata and configuration blocks must be loaded from external shared template files rather than duplicated as inline constants in each test file.
- The canonical service label for the outbound proxy node must reflect the correct workload-based name.

## Why This Matters

Without these updates, the end-to-end test suite fails to compile and run against the current dependencies, making it impossible to validate proxy behavior. Centralizing configuration in shared template files also reduces duplication and makes future maintenance easier.
