I'm working on the session replay feature in VS Code Copilot that shows Claude Code conversation history in the chat panel.

*   The ClaudeToolNames enum must include an Agent constant with the string value 'Agent', in addition to the existing Task constant.

*   createFormattedToolInvocation must handle the Agent tool name identically to the Task tool name: the returned object must have toolName equal to ClaudeToolNames.Agent ('Agent') and invocationMessage.value must contain the tool input's description field.

*   completeToolInvocation must handle the Agent tool name identically to Task: toolSpecificData must be populated with a ChatSubagentToolInvocationData instance where description comes from the tool input's description field, agentName comes from the tool input's subagent_type field, prompt comes from the tool input's prompt field, and result comes from the tool result content string.

*   The ISubagentSession interface must include an optional parentToolUseId field of type string, representing the ID of the parent Agent or Task tool_use block that spawned this subagent.

*   sdkSubagentMessagesToSubagentSession(agentId: string, messages: SessionMessage[]) must be exported from sdkSessionAdapter.ts. It must return null when messages is empty. For non-empty messages it must return an ISubagentSession including the agentId and a parentToolUseId extracted by scanning all assistant messages for a parent_tool_use_id field whose value is a string; non-string values must be ignored and parentToolUseId must be undefined if no valid string value is found.

*   buildClaudeCodeSession must accept folderName as the 4th positional parameter directly (not as a 5th parameter); the subagentCorrelation/correlation map parameter must be removed. The function must still set folderName on the returned session.

*   SubagentCorrelationMap must no longer be exported from sdkSessionAdapter.ts.

*   buildChatHistory must link subagent sessions to parent tool calls using ISubagentSession.parentToolUseId (matching it to the tool_use_id of tool_result blocks in user messages), not via a toolUseResultAgentId field on StoredMessage. Both 'Agent' and 'Task' tool names must be treated as valid parent tool types. Subagents whose parentToolUseId is undefined or not present must be excluded from injection and must not contribute tool parts to the response. When a subagent is matched, its tool calls must appear as ChatToolInvocationPart entries with subAgentInvocationId set to the parent's tool use ID.

*   getSession in ClaudeCodeSessionService must support loading sessions without a directory argument when operating in the agent sessions workspace mode. It must return undefined when the session is not found in the SDK and must return undefined when the SDK throws an error during loading.


*   Interface details: Type: Enum Value
Name: ClaudeToolNames.Agent
Location: extensions/copilot/src/extension/chatSessions/claude/common/claudeTools.ts
Description: New constant added to the ClaudeToolNames enum. Must have the string value 'Agent'. Represents the renamed subagent-spawning tool introduced in Claude Code SDK v2.1.63. The existing ClaudeToolNames.Task ('Task') must be retained for backward compatibility.

Type: Function
Name: sdkSubagentMessagesToSubagentSession
Location: extensions/copilot/src/extension/chatSessions/claude/node/sessionParser/sdkSessionAdapter.ts
Signature: sdkSubagentMessagesToSubagentSession(agentId: string, messages: readonly SessionMessage[]) -> ISubagentSession | null
Description: Converts an array of SDK SessionMessage objects into an ISubagentSession for display in the chat history. Returns null when the messages array is empty. For non-empty messages, returns an ISubagentSession that includes the provided agentId and a parentToolUseId extracted by scanning all messages for an assistant message whose parent_tool_use_id field is a string; non-string values are ignored and parentToolUseId is undefined if no string value is found. Must be exported.

Type: Function
Name: buildClaudeCodeSession
Location: extensions/copilot/src/extension/chatSessions/claude/node/sessionParser/sdkSessionAdapter.ts
Signature: buildClaudeCodeSession(info: SDKSessionInfo, messages: readonly SessionMessage[], subagents: readonly ISubagentSession[], folderName?: string) -> IClaudeCodeSession
Description: Assembles a full IClaudeCodeSession from SDK data. The subagentCorrelation/correlation map parameter has been removed. folderName is now the 4th positional parameter (previously 5th). The function must still set folderName on the returned session.

Type: Interface Field
Name: ISubagentSession.parentToolUseId
Location: extensions/copilot/src/extension/chatSessions/claude/node/sessionParser/claudeSessionSchema.ts
Description: Optional string field added to the ISubagentSession interface. Holds the tool_use_id of the Agent or Task tool_use block in the parent session that spawned this subagent. Used by buildChatHistory to link subagent tool calls under the correct parent tool invocation. Declaration: readonly parentToolUseId?: string

Type: Function (behavior extension)
Name: createFormattedToolInvocation
Location: extensions/copilot/src/extension/chatSessions/claude/common/toolInvocationFormatter.ts
Description: Existing function that must be extended to handle ClaudeToolNames.Agent the same way it handles ClaudeToolNames.Task. When the tool name is 'Agent', the returned object must have toolName equal to ClaudeToolNames.Agent and invocationMessage.value must contain the description from the tool input.

Type: Function (behavior extension)
Name: completeToolInvocation
Location: extensions/copilot/src/extension/chatSessions/claude/common/toolInvocationFormatter.ts
Description: Existing function that must be extended to handle ClaudeToolNames.Agent the same way it handles ClaudeToolNames.Task. When the tool name is 'Agent', toolSpecificData must be set to a ChatSubagentToolInvocationData instance with: description from input.description, agentName from input.subagent_type, prompt from input.prompt, result from the tool result content string.

Type: Function (behavior change)
Name: buildChatHistory
Location: extensions/copilot/src/extension/chatSessions/vscode-node/chatHistoryBuilder.ts
Description: Existing function whose subagent correlation logic must change. Instead of using toolUseResultAgentId on StoredMessage to look up subagents by agentId, it must use ISubagentSession.parentToolUseId to look up subagents by the tool_use_id of tool_result blocks in user messages. Subagents with undefined parentToolUseId must be excluded from injection entirely (contributing zero tool parts). When a subagent is matched, its tool calls must appear as ChatToolInvocationPart entries with subAgentInvocationId set to the parent tool use ID. Both 'Agent' and 'Task' tool names must be supported as parent tool types.

Type: Removed Export
Name: SubagentCorrelationMap
Location: extensions/copilot/src/extension/chatSessions/claude/node/sessionParser/sdkSessionAdapter.ts
Description: The SubagentCorrelationMap type alias (ReadonlyMap<string, string>) must be removed from this module's exports. Any code that imported it must be updated accordingly.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.