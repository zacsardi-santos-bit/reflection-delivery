## Description

When a user configures an MCP server and an installed extension also ships configuration for a server with the same name, the system currently ignores the extension's contribution entirely. This means extension-provided tool restrictions and environment variables are silently dropped, and the extension's metadata is never associated with the server entry. The extension and user configurations should instead be merged together using well-defined combination rules.

## Expected Behavior

- When both a user config and an extension define a server with the same name, the system should merge the two configurations and reconnect the server with the merged settings.
- Tool exclusion lists from both sides should be combined so that any tool excluded by either source is excluded overall.
- Tool inclusion lists (allowlists) should be intersected so only tools permitted by both sources remain. If only one side defines an allowlist, that list is honored in full. If the intersection is empty, no tools are permitted.
- Environment variables should be merged, with user-supplied values taking priority over extension-supplied values when both define the same key.
- User-supplied connection properties (such as server command and arguments) take precedence over those provided by the extension. Other properties supplied only by the base configuration are preserved.
- The extension's identity should always be preserved in the merged configuration.
- Merging must work correctly regardless of which source (user config or extension) is registered first.

## Why This Matters

Extensions that wrap or augment existing user-configured servers currently have no way to contribute their intended tool restrictions or environment settings. Silently ignoring the extension configuration leads to unexpected tool availability and missing runtime configuration, which makes extensions unreliable when server names overlap with the user's own configuration.
