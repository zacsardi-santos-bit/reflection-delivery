## Description

The Anthropic integration in Home Assistant currently supports web search as a server-side AI tool. We need to extend this to support two additional server-side capabilities: running shell commands and reading/editing files in isolated sandbox environments.

## Expected Behavior

- Users should be able to enable a new "code execution" option in the Anthropic integration configuration, which is disabled by default.
- When the AI uses these code execution tools during a conversation, the conversation history should correctly record both the action (tool call) and its result (tool result), including any output, error codes, or file contents.
- After a sandboxed environment is created during a conversation turn, the same environment must be reused in subsequent turns of that conversation so that any files or state from earlier turns remain available.
- The integration must correctly handle both successful code execution results and error responses from these tools.
- These new tool types must be serialized correctly when the conversation history is sent back to the AI in subsequent turns.

## Why This Matters

Many useful tasks — like running calculations, generating and saving data, or reading/editing files — require actual code execution rather than just text generation. Supporting these server-side tools lets the AI carry out complex multi-step tasks within a persistent session, greatly expanding what the assistant can do for users.
