Implement a set of testing utilities to facilitate integration-style component tests for GraphQL-powered UI components without the need for a real server. Create utilities to mock schemas, layer custom resolvers, and intercept network requests to route them through local schemas.

*   Implement `createMockSchema` in `src/testing/graphql-tools/utils.ts`:
    *   Accepts a `GraphQLSchema` and a `mocks` object mapping type names to mock factory functions.
    *   Returns a new `GraphQLSchema` with auto-resolved scalar fields using the provided mock functions.
    *   Ensure the schema handles standard scalars (ID, Int, String, Float), custom scalars, union types, interface types, and non-standard ID fields (e.g., _id).
    *   Ensure mock values are stable and consistent across multiple executions of the same query on the same schema instance.
    *   Export this function from both `src/testing/graphql-tools/utils.ts` and `src/testing/index.ts`.

*   Implement `createProxiedSchema` in `src/testing/core/createProxiedSchema.ts`:
    *   Accepts a mocked `GraphQLSchema` and a `Resolvers` object.
    *   Returns a `ProxiedSchema` with `.fork()` and `.add()` methods.
    *   Custom resolvers should override mock defaults, with missing fields falling back to scalar mock values.
    *   Export this function from `src/testing/core/index.ts`.

*   Define `ProxiedSchema` interface in `src/testing/core/createProxiedSchema.ts`:
    *   `fork(forkOptions?: { resolvers?: Resolvers }): ProxiedSchema`
        *   Creates and returns a new independent `ProxiedSchema` inheriting all resolvers from the parent.
        *   If `forkOptions.resolvers` is provided, these override inherited resolvers in the fork.
    *   `add(addOptions: { resolvers: Resolvers }): ProxiedSchema`
        *   Layers new resolvers onto the current schema, accumulating over multiple calls.
        *   Resolvers added are preserved when `.fork()` is called.

*   Implement `createMockFetch` in `src/testing/core/createMockFetch.ts`:
    *   Accepts a `GraphQLSchema` and optionally `mockFetchOpts`.
    *   Returns an object replacing `window.fetch` with a mock routing GraphQL requests to the schema.
    *   Expose a `restore()` function to reinstate the original fetch.
    *   Implement `Symbol.dispose` for compatibility with the `using` keyword for automatic cleanup.
    *   While active, intercept HTTP requests, execute GraphQL operations against the schema, and return results as JSON Responses.
    *   If called with an invalid (non-schema) value, return a response with an `ApolloError` containing schema validation errors.
    *   Ensure GraphQL errors thrown by resolvers are propagated as `graphQLErrors` in the response.

*   Export `createMockFetch`, `createMockSchema`, and `createProxiedSchema` from the testing package's public export list.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.