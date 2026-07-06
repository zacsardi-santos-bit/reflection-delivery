## Description

The DGS GraphQL framework does not properly handle malformed or empty query content in POST requests to the `/graphql` endpoint. When clients send requests with empty bodies, whitespace-only content, malformed JSON, or valid JSON with missing/empty query fields, the server's behavior is inconsistent and can result in unhelpful error messages or exceptions.

## Expected Behavior

- When a POST request has an empty or whitespace-only body, the server should return HTTP 400 Bad Request with the message "Invalid query - No content to map to input."
- When a POST request contains malformed JSON (e.g., `{`), the server should return HTTP 400 Bad Request with a message starting with "Invalid query -"
- When a POST request contains valid JSON but with an empty or null query field (e.g., `{ }`), the server should return HTTP 200 OK with a proper GraphQL error response containing:
  - Error message: "The query is null or empty."
  - Error type: "BAD_REQUEST" in the extensions
- The DgsContext should be accessible from the GraphQL context using a well-defined namespace key

## Current Behavior

- Empty or malformed request bodies may cause unhandled exceptions
- Error messages are inconsistent between WebMVC and WebFlux implementations
- The `BaseDgsQueryExecutor.baseExecute()` function throws an `IllegalArgumentException` when the query is null or empty instead of returning a proper GraphQL error
- The DgsContext namespace key is hardcoded as "dgs" rather than using a defined constant
