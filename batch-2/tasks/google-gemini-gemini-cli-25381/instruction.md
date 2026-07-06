I'm seeing spurious error diagnostics when connecting to certain MCP servers that don't support prompt listing.

*   The discoverPrompts function must be exported from packages/core/src/tools/mcp-client.ts.

*   When discoverPrompts is called and the MCP client returns a MethodNotFound error (using the proper McpError type with ErrorCode.MethodNotFound), the function must return an empty array without emitting any MCP diagnostic.

*   The MethodNotFound error detection in discoverPrompts must be based on the error's type and numeric error code (McpError instance with ErrorCode.MethodNotFound), not on string matching of the error message text. Any McpError carrying a MethodNotFound code must be silently handled regardless of its message content.


*   Interface details: Type: Function
Name: discoverPrompts
Location: packages/core/src/tools/mcp-client.ts
Signature: discoverPrompts(mcpServerName: string, client: Client, cliConfig: McpContext) -> Promise<...>
Description: Discovers and returns a list of prompts from a connected MCP client. Must be exported. When the client throws a McpError with ErrorCode.MethodNotFound, must return an empty array without emitting any diagnostic. For other errors, emits a diagnostic via cliConfig.emitMcpDiagnostic and returns an empty array.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.