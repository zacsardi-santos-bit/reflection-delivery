## Description

We need to add integration with external embedding API services (such as OpenAI) to pgvecto.rs so that users can generate vector embeddings from text directly within the database without needing a separate external pipeline.

Currently, the extension has no way to call out to a remote embedding service. We need a dedicated crate that handles the HTTP communication, request/response serialization, and error handling for these embedding API calls.

## Expected Behavior

- A new workspace crate should be added that encapsulates all logic for calling an external embedding API
- The crate should provide a function that accepts a text input, a model name, and connection options (base URL and API key), sends a request to the embedding service, and returns the resulting vector
- When the service returns a valid embedding response, the function should return success with the vector data
- When the service response contains no embeddings (empty data array), attempting to extract the embedding should return an error
- When the service is unreachable or returns a non-parseable response (such as an HTTP error), the function should return an error

## Why This Matters

This enables in-database text-to-vector conversion by delegating to external embedding models. Users and applications can embed text at query time without managing a separate embedding service or preprocessing pipeline outside the database. Proper error handling for all failure modes (network errors, empty responses, malformed responses) ensures the integration is robust for production use.
