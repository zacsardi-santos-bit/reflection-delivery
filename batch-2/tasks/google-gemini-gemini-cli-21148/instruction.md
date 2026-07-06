Update the MCP server listing feature to support new server statuses: "Blocked" and "Disabled." Ensure that servers are correctly categorized and displayed based on their status, and update the function signature to accept a comprehensive settings object.

*   Modify the `listMcpServers` function in `packages/cli/src/commands/mcp/list.ts`:
    *   Change the function signature to `listMcpServers(loadedSettingsArg?: LoadedSettings): Promise<void>`.
    *   Accept an optional `LoadedSettings` argument, which includes `{ merged: MergedSettings, isTrusted: boolean }`.
    *   Call `loadSettings()` to obtain settings if no argument is provided.
    *   Log servers in the `mcp.excluded` list with the format '{serverName}: {command}  (stdio) - Blocked' and avoid connecting to them.
    *   Use `McpServerEnablementManager.getInstance().isFileEnabled(serverName)` to determine if a server is disabled. Log with '{serverName}: {command}  (stdio) - Disabled' and avoid connecting.

*   Update the `MCPServerStatus` enum in `packages/core/src/tools/mcp-client.ts`:
    *   Add `BLOCKED` and `DISABLED` statuses alongside existing values (CONNECTED, CONNECTING, DISCONNECTED).

*   Utilize the `McpServerEnablementManager` class in `packages/cli/src/config/mcp/index.ts`:
    *   Ensure the class provides `getInstance()`, `resetInstance()`, and `isFileEnabled(serverName: string): Promise<boolean>`.
    *   Use `getInstance()` to get the singleton and `isFileEnabled()` to check server enablement.

*   Adjust the `McpStatus` React component in `packages/cli/src/ui/components/views/McpStatus.tsx`:
    *   Filter out servers listed in the `blockedServers` prop from the regular server list.
    *   Ensure servers in `blockedServers` appear only in the blocked section.
    *   Render the "Configured MCP servers:" header and blocked server entries when `servers` is empty but `blockedServers` is not.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.