I'm working on adding telemetry to track tool invocations in Claude Code chat sessions.

*   The MessageHandlerState type must include a new field toolStartTimes of type Map<string, number> that maps tool call IDs to the timestamp (in milliseconds) when the tool was invoked.

*   When handleUserMessage processes a tool_result block that matches an entry in state.unprocessedToolCalls, it must call ITelemetryService.sendMSFTTelemetryEvent with event name 'languageModelToolInvoked'.

*   The telemetry event properties must include: result ('success', 'error', or 'userCancelled'), chatSessionId (formatted as 'claude-code:/<sessionId>'), toolId (the tool name from the matched tool_use block), toolExtensionId (always undefined), and toolSourceKind ('claudeCode' or 'mcp').

*   The toolSourceKind property must be 'mcp' when the tool name starts with 'mcp__', and 'claudeCode' for all other tools.

*   The result property must be 'error' when the tool_result block has is_error set to true, 'userCancelled' when the tool_result content equals the DENY_TOOL_MESSAGE constant, and 'success' otherwise.

*   The telemetry measurements argument must be { invocationTimeMs: number } (a non-negative number) when state.toolStartTimes contains an entry for the tool call ID. When no timing entry is present, the measurements argument must be undefined.

*   No telemetry event must be emitted for tool_result blocks that have no matching entry in state.unprocessedToolCalls.

*   ITelemetryService must be retrievable from the ServicesAccessor using the ITelemetryService injection token so that handleUserMessage can emit tool invocation telemetry events.


*   Interface details: Type: Interface (modified)
Name: MessageHandlerState
Location: extensions/copilot/src/extension/chatSessions/claude/common/claudeMessageDispatch.ts
Description: Mutable state threaded through message handler functions. Must be extended with a new field to track when each tool invocation started.
Fields:
  toolStartTimes: Map<string, number>  — maps tool call ID to the timestamp (ms) at which the tool was invoked. Used to compute invocation duration when a tool result is received.

---

Type: Function (modified behavior)
Name: handleUserMessage
Location: extensions/copilot/src/extension/chatSessions/claude/common/claudeMessageDispatch.ts
Description: Processes incoming user messages (tool results). Must now emit a telemetry event for each tool_result block that matches an entry in state.unprocessedToolCalls.
Telemetry call signature: ITelemetryService.sendMSFTTelemetryEvent(eventName, properties, measurements?)
  - eventName: 'languageModelToolInvoked'
  - properties: { result: 'success' | 'error' | 'userCancelled', chatSessionId: string, toolId: string, toolExtensionId: undefined, toolSourceKind: 'claudeCode' | 'mcp' }
  - measurements: { invocationTimeMs: number } when state.toolStartTimes has an entry for the tool call ID, otherwise undefined

Property derivation rules:
  - result: 'error' when the tool_result block has is_error: true; 'userCancelled' when the content equals the DENY_TOOL_MESSAGE constant; 'success' otherwise
  - toolSourceKind: 'mcp' when the tool name starts with 'mcp__'; 'claudeCode' otherwise
  - chatSessionId: formatted as 'claude-code:/<sessionId>' where sessionId is the current session identifier
  - toolId: the name field of the matched tool_use block
  - toolExtensionId: always undefined

No telemetry must be emitted for tool_result blocks that have no matching entry in state.unprocessedToolCalls.

---

Type: Interface (existing, referenced)
Name: ITelemetryService
Location: extensions/copilot/src/platform/telemetry/common/telemetry.ts
Description: Service for sending Microsoft telemetry events. Must be retrieved from the ServicesAccessor using the ITelemetryService injection token and used to emit tool invocation events.
Signature: sendMSFTTelemetryEvent(eventName: string, properties?: TelemetryEventProperties, measurements?: TelemetryEventMeasurements): void


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.