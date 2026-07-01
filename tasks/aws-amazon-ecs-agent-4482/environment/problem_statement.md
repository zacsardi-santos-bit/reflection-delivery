## Description

The ECS agent's introspection server currently lacks a clean, standalone package with a well-defined API for constructing and configuring the server. There is no way to programmatically create the server with options such as custom read/write timeouts or optional profiling endpoint exposure. Additionally, there is no structured typed error system that consistently maps backend failures to the correct HTTP status codes and operational metrics.

## Expected Behavior

- A new introspection server should be constructable programmatically and must validate that required dependencies (agent state and metrics factory) are non-nil, returning a descriptive error if either is absent.
- The server must support optional configuration including read timeout, write timeout, and whether profiling endpoints are enabled.
- When profiling is disabled (the default), profiling-related paths should not be exposed, and the server root must only list the non-profiling endpoints. When profiling is enabled, the profiling paths should be included and the write timeout should be overridden to accommodate long-running profile requests.
- The server must expose endpoints for agent metadata, task metadata (retrievable by full container identifier, short container identifier, or task ARN), and license text.
- Typed errors must be introduced so that different failure conditions (resource not found, fetch failure, multiple conflicting results) are mapped to the correct HTTP status codes (404, 500, 400 respectively) and emit the appropriate operational metrics.
- A panic occurring within any request handler must result in a 500 response with an empty body and emission of a crash metric.
- A utility for writing plain-text responses must set the content type to plain text.

## Why This Matters

Without a structured server package and typed errors, introspection failures cannot be accurately classified, making it hard to differentiate user errors from internal problems and to monitor the health of the introspection service. A configurable server API also makes testing and integration significantly cleaner.
