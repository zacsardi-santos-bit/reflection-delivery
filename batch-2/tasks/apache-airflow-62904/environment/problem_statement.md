## Description

Airflow currently has no native integration for connecting to external tool servers that implement the Model Context Protocol (MCP). Without this, developers who want to use MCP-compatible tool providers in their AI agent tasks have no standard way to manage those connections through Airflow's connection management infrastructure. This creates friction for teams building AI-powered workflows that rely on MCP servers.

## Expected Behavior

- A new connection hook should be available in Airflow that lets users configure connections to MCP servers through the Airflow UI, supporting HTTP, SSE, and stdio transports.
- The connection UI should hide irrelevant fields (schema, port, login) and relabel the password field as "Auth Token" to match the MCP authentication model.
- When a host URL is required but not configured, the system should produce a clear error message indicating that a host URL is needed.
- For stdio-based connections, a clear error should be raised when no command is specified.
- An unknown or unsupported transport value should raise an error indicating the transport type is unrecognized.
- A companion toolset adapter should wrap the hook and expose the server's tools to AI agent tasks, with an identifier derived from the connection ID.
- Both the hook and toolset should cache their server connection object so it is only created once per instance.
- The toolset should delegate tool listing and tool invocation to the underlying MCP server.

## Why This Matters

Teams building AI agent workflows in Airflow need a reliable, idiomatic way to connect to MCP tool servers. Supporting multiple transport types and providing clear validation errors helps developers quickly discover and fix misconfigured connections without digging into internals.
