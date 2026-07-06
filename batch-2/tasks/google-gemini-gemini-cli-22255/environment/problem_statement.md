## Description

There are two related issues with the policy update mechanism in the tool confirmation flow:

1. **Incorrect MCP server tool name pattern**: When a user selects "always allow for this server" for an MCP tool, the system broadcasts a policy update that should match all tools from that server using a wildcard. However, the tool name pattern currently used does not match the actual naming convention for MCP tools in the rest of the system. This mismatch means the saved policy may not correctly apply to future tool invocations from that server.

2. **Implicit message bus dependency**: The function responsible for updating policy currently receives the message bus through a shared context object rather than as a direct, explicit parameter. This makes the dependency implicit and harder to reason about. The message bus should be passed directly as its own argument.

## Expected Behavior

- When a server-scope "always allow" policy is recorded for an MCP server, the tool name pattern in the published message must use the correct naming format that matches how MCP tools are identified throughout the system.
- The policy update function should accept the message bus as an explicit, separate parameter rather than expecting it to be embedded inside a broader context object.

## Why This Matters

These issues affect reliability of the "always allow" approval mode for MCP server tools. If the tool name pattern is wrong, previously approved servers may still prompt for confirmation on subsequent calls. The refactoring also improves code clarity by making function dependencies explicit.
