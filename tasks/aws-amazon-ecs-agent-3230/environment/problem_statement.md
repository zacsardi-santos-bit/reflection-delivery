## Description

The ECS agent currently uses hardcoded internal API endpoint URLs to communicate with the service connect proxy: one endpoint for fetching metrics and another for draining connections. This works only as long as the proxy's API surface remains unchanged. There is no mechanism to override or configure these endpoints on a per-task basis.

## Expected Behavior

- The task runtime configuration should accept configurable URLs for both the stats retrieval and connection drain operations, so the agent can direct requests to the correct proxy-specific endpoints.
- When the service connect manager sets up a task's agent container, the stats and drain endpoint URLs it knows about should be stored in the task's runtime configuration and accessible after setup.
- Service connect containers should automatically receive an environment variable that disables IAM authentication for XDS connections.
- The relay socket path passed to the service connect container as an environment variable should use the correct Unix socket URI format (with the appropriate scheme prefix).

## Why This Matters

Hardcoding proxy API endpoints prevents the system from adapting when the proxy changes its API surface or when different proxy versions use different endpoint paths. Making these URLs part of the runtime configuration allows the system to remain flexible and forward-compatible without requiring code changes for each endpoint variation.
