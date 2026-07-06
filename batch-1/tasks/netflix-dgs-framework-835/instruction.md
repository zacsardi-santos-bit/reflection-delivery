Implement consistent error handling for malformed or empty query content in the Netflix DGS GraphQL framework. Ensure that the server returns appropriate HTTP status codes and error messages for different types of invalid requests to the `/graphql` endpoint.

*   Update the `BaseDgsQueryExecutor` object:
    *   Move `BaseDgsQueryExecutor` to `graphql-dgs/src/main/kotlin/com/netflix/graphql/dgs/internal/BaseDgsQueryExecutor.kt`.
    *   Modify the `baseExecute` function to accept a nullable `query` parameter and handle null or empty queries gracefully.
    *   Return an `ExecutionResult` containing a GraphQL error with message "The query is null or empty." and errorType 'BAD_REQUEST' in the extensions when the query is null or empty.

*   Handle POST requests to `/graphql`:
    *   Return HTTP 400 Bad Request with the message "Invalid query - No content to map to input." for empty or whitespace-only body content.
    *   Return HTTP 400 Bad Request with a message starting with "Invalid query -" for malformed JSON content.
    *   Return HTTP 200 OK with a GraphQL error response containing the message "The query is null or empty." and errorType 'BAD_REQUEST' in the extensions for valid JSON with an empty query field.

*   Update the `DgsContext` class:
    *   Provide a constant `GRAPHQL_CONTEXT_NAMESPACE_KEY` with the value "netflix.graphql.dgs".
    *   Implement a static method `getDgsContext(graphQLContext: GraphQLContext): DgsContext` to retrieve the `DgsContext` from the GraphQL context using the `GRAPHQL_CONTEXT_NAMESPACE_KEY`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.