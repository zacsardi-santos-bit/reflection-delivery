Implement structured tracing instrumentation for the Gemini CLI to enable observability of its operations using standard monitoring tools. Create active spans for key operations, capturing inputs, outputs, and errors with standardized attributes. Ensure tracing is always active and handle streaming operations appropriately.

*   Implement the `runInDevTraceSpan` function in `packages/core/src/telemetry/trace.ts`:
    *   Accept an options object with 'operation' (a `GeminiCliOperation` enum value) and an optional 'noAutoEnd' boolean.
    *   Accept a callback function that receives a span object with 'metadata' (`SpanMetadata`) and 'endSpan' callback.
    *   Return a Promise of the callback's return value.
    *   Create an active OpenTelemetry span using the operation value as the span name.
    *   Populate `metadata.attributes` with default values:
        *   `GEN_AI_OPERATION_NAME` set to the operation value.
        *   `GEN_AI_AGENT_NAME` set to `SERVICE_NAME`.
        *   `GEN_AI_AGENT_DESCRIPTION` set to `SERVICE_DESCRIPTION`.
        *   `GEN_AI_CONVERSATION_ID` set to the current session ID.
    *   On successful callback resolution (with `noAutoEnd` not set):
        *   Serialize `metadata.input` as JSON to `GEN_AI_INPUT_MESSAGES`.
        *   Serialize `metadata.output` as JSON to `GEN_AI_OUTPUT_MESSAGES`.
        *   Set additional `metadata.attributes` as span attributes.
        *   Set span status to `SpanStatusCode.OK` and call `span.end()`.
    *   On callback error:
        *   Set span status to `SpanStatusCode.ERROR` with the error message.
        *   Call `span.recordException(error)` and `span.end()`.
        *   Re-throw the error.
    *   If `noAutoEnd` is true, do not call `span.end()` automatically; use `endSpan` to end the span manually.
    *   If span attribute setting throws an exception during finalization, call `diag.error()`, set span status to `SpanStatusCode.ERROR`, and call `span.end()`.

*   Define `SpanMetadata` interface in `packages/core/src/telemetry/trace.ts`:
    *   Fields: `name` (optional string), `attributes` (Record<string, any>), `input` (optional any), `output` (optional any), `error` (optional any).

*   Export `GeminiCliOperation` enum from `packages/core/src/telemetry/constants.ts` with values: `LLMCall`, `ToolCall`, `UserPrompt`, `SystemPrompt`, `AgentCall`, `ScheduleToolCalls`.

*   Export telemetry attribute constants from `packages/core/src/telemetry/constants.ts`:
    *   `SERVICE_NAME`, `SERVICE_DESCRIPTION`, `GEN_AI_OPERATION_NAME`, `GEN_AI_AGENT_NAME`, `GEN_AI_AGENT_DESCRIPTION`, `GEN_AI_INPUT_MESSAGES`, `GEN_AI_OUTPUT_MESSAGES`, `GEN_AI_CONVERSATION_ID`, `GEN_AI_REQUEST_MODEL`, `GEN_AI_PROMPT_NAME`, `GEN_AI_SYSTEM_INSTRUCTIONS`, `GEN_AI_TOOL_DEFINITIONS`, `GEN_AI_USAGE_INPUT_TOKENS`, `GEN_AI_USAGE_OUTPUT_TOKENS`, `GEN_AI_TOOL_NAME`, `GEN_AI_TOOL_CALL_ID`, `GEN_AI_TOOL_DESCRIPTION`.

*   Update the content generator wrapper:
    *   `generateContent` method should call `runInDevTraceSpan` with `GeminiCliOperation.LLMCall` and initial attributes including `GEN_AI_REQUEST_MODEL`, `GEN_AI_PROMPT_NAME`, `GEN_AI_SYSTEM_INSTRUCTIONS`, `GEN_AI_TOOL_DEFINITIONS`.
    *   On success, set span metadata `input` to `req.contents`, `output` to the first candidate's content, and token usage attributes.
    *   On failure, set `metadata.error` to the thrown error.
    *   `generateContentStream` method should call `runInDevTraceSpan` with `noAutoEnd: true` and similar attributes.
    *   On stream completion, set span metadata `input` to `req.contents`, `output` to an array of candidate contents, and token usage attributes.
    *   `embedContent` method should call `runInDevTraceSpan` with `GeminiCliOperation.LLMCall` and `GEN_AI_REQUEST_MODEL` attribute.
    *   On success, set span metadata `input` to `req.contents` and `output` to the full embed response.

*   Update the tool scheduler:
    *   Call `runInDevTraceSpan` with `GeminiCliOperation.ScheduleToolCalls` when scheduling tool calls, setting `metadata.input` to the array of tool call requests.

*   Update the tool executor:
    *   Call `runInDevTraceSpan` with `GeminiCliOperation.ToolCall` and initial attributes including `GEN_AI_TOOL_NAME`, `GEN_AI_TOOL_CALL_ID`, `GEN_AI_TOOL_DESCRIPTION`.
    *   On success, set `metadata.input` to the scheduled call request and `metadata.output` to the tool result with `durationMs` and `endTime`.
    *   On error, set `metadata.error` to the thrown error.

*   Update sub-agent invocation:
    *   Call `runInDevTraceSpan` with `GeminiCliOperation.AgentCall` and initial attributes including `GEN_AI_AGENT_NAME` and `GEN_AI_AGENT_DESCRIPTION`.
    *   Set span metadata `input` to invocation params and `output` to invocation result.

*   Update stream processing hook:
    *   Call `runInDevTraceSpan` with `GeminiCliOperation.SystemPrompt` for system prompts, setting `metadata.input`.
    *   Call `runInDevTraceSpan` with `GeminiCliOperation.UserPrompt` for user prompts, setting `metadata.input` to the query string.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.