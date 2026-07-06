I'm working on an agent host server that manages sessions with an AI backend.

*   AgentSideEffects.handleRestoreSession must be a no-op (return without error) when the session is already registered in the state manager.

*   AgentSideEffects.handleRestoreSession must throw an error whose message matches /No agent for session/ when no agent can be found for the given session URI.

*   AgentSideEffects.handleRestoreSession must throw an error whose message matches /Session not found on backend/ when the session URI does not appear in the agent's listSessions result.

*   AgentSideEffects.handleRestoreSession must reconstruct turns from the agent's message history: each consecutive user-message followed by an assistant-message pair produces one turn with TurnState.Complete, the user message text in userMessage.text, and the assistant message text in responseText.

*   AgentSideEffects.handleRestoreSession must handle multiple turns: N user+assistant message pairs must produce N turns in the restored session state.

*   AgentSideEffects.handleRestoreSession must handle interrupted turns: when a user message is not followed by an assistant message before the next user message arrives, the open turn must be flushed as TurnState.Cancelled with an empty responseText, and a new turn must be started for the next user message.

*   AgentSideEffects.handleRestoreSession must restore tool calls within a turn with ToolCallStatus.Completed and ToolCallConfirmationReason.NotNeeded, preserving toolCallId, toolName, displayName, and success fields from the agent's tool_start and tool_complete events.

*   AgentSideEffects.handleRestoreSession must populate responseParts for each assistant message as a single part with kind === ResponsePartKind.Markdown and content equal to the assistant message text.

*   AgentSideEffects.handleRestoreSession must preserve the workingDirectory from the agent session metadata (IAgentSessionMetadata) in the restored state's summary.workingDirectory field.

*   AgentSideEffects.handleRestoreSession must use SessionStateManager.restoreSession (not createSession) to register the session, ensuring the session is created in SessionLifecycle.Ready state.

*   SessionStateManager.restoreSession must return a session state with lifecycle === SessionLifecycle.Ready and turns pre-populated from the provided turns array.

*   SessionStateManager.restoreSession must return the existing session state unchanged when called for a session that is already registered in the state manager.

*   SessionStateManager.restoreSession must NOT emit a sessionAdded notification, unlike createSession.

*   IProtocolSideEffectHandler must declare a handleRestoreSession(session: string): Promise<void> method so that all side-effect handler implementations are required to provide it.

*   When a client subscribes to a session that was not created through handleCreateSession but is present in the agent backend's listSessions, the server must trigger the restore path and return a snapshot with lifecycle 'ready', reconstructed turns (including tool calls and response text), and must not emit a sessionAdded notification to connected clients.


*   Interface details: Type: Method
Name: handleRestoreSession
Location: src/vs/platform/agentHost/node/agentSideEffects.ts
Signature: handleRestoreSession(session: string): Promise<void>
Description: Restores a session from a previous server lifetime into the state manager. Fetches message history from the agent backend, reconstructs turns, and registers the session via restoreSession. Is a no-op if the session is already in the state manager. Throws an error matching /No agent for session/ when no agent is found. Throws an error matching /Session not found on backend/ when the session is not listed by the agent backend.

Type: Method
Name: restoreSession
Location: src/vs/platform/agentHost/node/sessionStateManager.ts
Signature: restoreSession(summary: ISessionSummary, turns: ITurn[]): ISessionState
Description: Creates a session in SessionLifecycle.Ready state with the provided turns pre-populated. If the session already exists in the state manager, returns the existing state unchanged. Does NOT emit a sessionAdded notification (unlike createSession).

Type: Interface method
Name: handleRestoreSession
Location: src/vs/platform/agentHost/node/protocolServerHandler.ts
Signature: handleRestoreSession(session: string): Promise<void>
Description: Must be added to the IProtocolSideEffectHandler interface. Delegates session restoration to the underlying agent side-effects implementation.

Type: Constant
Name: PRE_EXISTING_SESSION_URI
Location: src/vs/platform/agentHost/test/node/mockAgent.ts
Signature: export const PRE_EXISTING_SESSION_URI: URI
Description: A well-known URI for a pre-existing session used in integration tests. Created via AgentSession.uri('mock', 'pre-existing-session'). Exported so integration tests can reference it without hard-coding the URI string.

Type: Class field
Name: sessionMessages
Location: src/vs/platform/agentHost/test/node/mockAgent.ts (MockAgent class)
Signature: sessionMessages: (IAgentMessageEvent | IAgentToolStartEvent | IAgentToolCompleteEvent)[]
Description: Configurable return value for getSessionMessages() in MockAgent. Defaults to empty array. When set, getSessionMessages() returns this array.

Type: Class field
Name: sessionMetadataOverrides
Location: src/vs/platform/agentHost/test/node/mockAgent.ts (MockAgent class)
Signature: sessionMetadataOverrides: Partial<Omit<IAgentSessionMetadata, 'session'>>
Description: Optional metadata overrides applied to every session returned by MockAgent.listSessions(). Spread onto each session object after the base fields.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.