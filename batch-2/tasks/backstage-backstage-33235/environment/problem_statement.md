## Description

The MCP actions backend currently exposes all registered actions as a flat list of tools at a single endpoint, using only the bare action name without any plugin prefix. This makes it impossible to tell which plugin an action originates from, and there is no way to partition actions into separate, focused server endpoints.

We need two improvements:

1. **Namespaced tool names**: By default, tool names should include a plugin prefix so that the source of each tool is clear to MCP clients. The format should combine the originating plugin's ID and the action name. This namespacing should be opt-out for backward compatibility.

2. **Multi-server configuration**: Operators should be able to configure multiple MCP server endpoints, each scoped to a subset of actions. Filtering should support matching on action IDs (with glob patterns) and on action attributes (such as whether an action is destructive or read-only). Each endpoint should only expose and execute actions within its permitted set — calls targeting out-of-scope actions must return a "not found" error.

## Expected Behavior

- Tool names default to a namespaced format combining the plugin ID and the action name.
- Namespacing can be disabled to restore the bare action name format.
- When multiple servers are configured, each server gets its own endpoint and only lists tools within its scope.
- Glob-style patterns must be supported for include filters, allowing matching of all actions from a given plugin or actions with a name matching a prefix.
- Attribute-based exclude filters must be supported, allowing operators to exclude all destructive actions, for example.
- Calling a filtered-out tool on a server returns an error indicating the action was not found.

## Why This Matters

This allows teams to expose purpose-built endpoints to different consumers, with clear scoping and namespacing that prevents confusion and accidental execution of unintended actions.
