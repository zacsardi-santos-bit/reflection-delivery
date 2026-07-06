We have a growing test suite for WebAssembly function-calling behaviors, but many of those tests only run against one compiler backend. There's a separate file with some hand-duplicated tests for the alternative backend, but it only covers a fraction of the scenarios and is tedious to keep in sync.

I'd like to convert the existing tests to use our shared test annotation, which generates separate test variants for each supported compiler backend automatically. Some of these tests also require specific WebAssembly features to be enabled — right now they set those up inline, but the annotation should be able to declare the required features as an attribute so the framework handles enabling them and skipping the test for backends that don't support those features.

Once the shared annotation is extended to support this declarative feature flag syntax, the duplicated backend-specific tests in the standalone file should be removed since they'll be covered by the general suite.
