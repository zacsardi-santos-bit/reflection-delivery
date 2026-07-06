## Description

The AWS HealthOmics MCP server currently has no tools for managing run caches. Run caches allow genomics workflow runs to store and reuse computation results, which can significantly reduce both cost and execution time for repeated runs. Without these tools, users cannot create, inspect, list, or update run caches through the MCP server.

## Expected Behavior

The server should expose four new operations for run cache management:

- **Create**: Accept a caching strategy and a cloud storage location as required inputs, along with optional metadata (name, description, tags, and owner account). Before creating the cache, the tool should validate the storage location format and verify the bucket is accessible. If the bucket does not exist, the user should receive a clear error indicating the bucket was not found; if access is denied, they should receive an error indicating access was denied; if another error occurs accessing the bucket, the message should describe the issue with accessing that specific bucket. Invalid caching strategies should be rejected before any remote calls are made. Successful creation returns the cache identifier, ARN, and status.
- **Get**: Retrieve all details for a specific cache by ID. Any time-based fields in the response should be serialized as ISO 8601 strings.
- **List**: Return a collection of caches under a designated key, with optional filtering by name, status, or caching strategy, and pagination support. The pagination token should be present in the output only when the underlying service includes one.
- **Update**: Modify an existing cache's caching strategy, name, or description. Only the fields actually provided should be forwarded to the service. Returns the cache ID and a status indicating the update was successful.

All tools should return a structured error dictionary (with an error field whose value includes the error message text) when the underlying service raises an exception, rather than propagating the exception directly.

## Why This Matters

Without these tools, anyone using the MCP server to orchestrate genomics workflows has no way to leverage the run cache feature, even though the underlying service supports it. Adding these operations completes the server's coverage of core workflow management capabilities.
