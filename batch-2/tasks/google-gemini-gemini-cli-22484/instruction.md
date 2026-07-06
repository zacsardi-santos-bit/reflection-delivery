Implement a merging mechanism for MCP server configurations in the `McpClientManager` class. Ensure that when both user and extension configurations exist for the same server name, they are combined according to specified rules, and the server is reconnected with the merged settings.

*   Update `McpClientManager` in `packages/core/src/tools/mcp-client-manager.ts` to handle merging of configurations.
    *   Modify `startExtension(extension: GeminiCLIExtension): Promise<void>`:
        *   Disconnect the existing client if `extension.mcpServers` contains a server name already registered.
        *   Merge the extension's configuration with the user's configuration using defined rules.
        *   Reconnect using a new `McpClient` with the merged configuration.
    *   Modify `maybeDiscoverMcpServer(serverName: string, config: McpServerConfig): Promise<void>`:
        *   Disconnect the existing client if `serverName` is already registered.
        *   Merge the incoming configuration with the existing one using defined rules.
        *   Reconnect using a new `McpClient` with the merged configuration.

*   Ensure the merged configuration follows these rules:
    *   `excludeTools`: Combine both lists into a union.
    *   `includeTools`: Intersect both lists; if only one list is provided, use it entirely. If intersection is empty, result is an empty array.
    *   `env`: Perform a shallow merge, prioritizing user-supplied values for conflicts; include non-conflicting keys from both.
    *   Connection properties (e.g., `command`, `args`): Prioritize user-supplied values; preserve properties only in the base config.
    *   `extension`: Retain the `GeminiCLIExtension` object from the extension.

*   Ensure merging is consistent regardless of registration order (user or extension first).

*   When re-merging, disconnect the existing `McpClient` exactly once and create a new one with the merged configuration.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.