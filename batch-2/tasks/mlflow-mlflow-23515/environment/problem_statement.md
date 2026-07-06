# Add MCP Server Registry to the Database-Backed Tracking Store

## Description

MLflow needs a persistence layer for managing a registry of remote AI tool servers. Currently there is no way to store, version, or query these server configurations in the database. We need a full registry implementation backed by the existing database tracking store so that users can register servers, publish versioned configurations, control their lifecycle, and bind endpoint URLs to specific versions or named aliases.

## Expected Behavior

- Users can create, retrieve, update, and delete entries for remote tool servers. Duplicate server names or empty names are rejected with appropriate errors.
- Server configurations can be versioned. Each version tracks its lifecycle status (draft, active, deprecated, or deleted) and can carry structured tool definitions and metadata. Deleted versions are soft-deleted and excluded from all queries.
- Status can only transition through allowed paths. Attempting invalid transitions is rejected. A version cannot be directly deleted while active — it must first be moved to an intermediate state.
- Servers can have named aliases that point to specific versions, making it easy to reference a logical channel (e.g. "stable") rather than a hard-coded version string. The alias "latest" is reserved and always resolves automatically. Aliases are cleaned up when the version they point to is deleted.
- Access bindings associate endpoint URLs and transport types with a specific version or alias. Bindings referencing deleted versions or aliases are hidden or cleaned up. Each binding exposes the resolved concrete version it points to.
- Servers and server versions support key-value tags for metadata. Tags can be added, updated, or deleted.
- Search operations for servers, versions, and bindings support pagination, filtering (by name, tag, status, access binding presence, transport type), and ordering.
- Cascading deletes ensure that when a server is deleted, all its versions, tags, aliases, and bindings are also removed.

## Why This Matters

This registry provides the data layer that allows MLflow to catalog and serve information about remote tool servers, enabling downstream features that depend on discovering and connecting to these servers.
