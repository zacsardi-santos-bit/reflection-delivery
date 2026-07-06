## Description

Writing integration tests for GraphQL-powered UI components currently requires either mocking low-level network plumbing or spinning up a real server. Neither option is great: low-level mocks skip too much of the actual client stack, and a real server adds too much infrastructure overhead. What's missing is a set of testing utilities that let developers stand up a realistic, controllable GraphQL data layer in-process and run full component tests against it.

## Expected Behavior

- Developers should be able to create a schema where every scalar type is automatically given a sensible placeholder value, and custom scalars (like dates) can supply their own placeholder. The mocked values should be consistent across repeated queries on the same schema instance.
- On top of a mocked schema, developers should be able to layer custom resolvers that override specific fields — and any fields not covered by the custom resolver should fall back to the scalar defaults.
- That layered schema should support "forking" — creating an independent copy for a specific test without affecting the original. It should also support incrementally adding more resolvers to either the original or any fork.
- A companion utility should intercept the global network fetch during a test and route any GraphQL requests through the local schema instead of hitting a real server. It should clean up automatically (restoring the original fetch) when the test block exits.
- If an invalid object is passed as the schema, the intercepting utility should surface a meaningful validation error as a GraphQL error in the response, rather than crashing silently.
- GraphQL errors thrown inside resolvers should be correctly propagated to the client as GraphQL errors.

## Why This Matters

These utilities together allow developers to write realistic component tests that exercise the full Apollo Client link chain — including queries, mutations, error handling, and schema validation — without any backend infrastructure. Tests become faster, more reliable, and easier to isolate.
