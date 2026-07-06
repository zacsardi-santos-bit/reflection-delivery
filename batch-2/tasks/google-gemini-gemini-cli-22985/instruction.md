Create a dedicated module to translate raw model stream events into structured agent events, ensuring the translation logic is isolated and independently testable. Implement functions to handle various event types, maintain state across events, and update the agent event type to a discriminated union for type narrowing.

*   Create a new module at `packages/core/src/agent/event-translator.ts` with the following exports:
    *   `createTranslationState(streamId?: string): TranslationState`
    *   `translateEvent(event: ServerGeminiStreamEvent, state: TranslationState): AgentEvent[]`
    *   `mapFinishReason(reason?: FinishReason): StreamEndReason`
    *   `mapHttpToGrpcStatus(status?: number): string`
    *   `mapError(error: unknown): { status: string; message: string; fatal: boolean; _meta?: Record<string, unknown> }`
    *   `mapUsage(metadata: { promptTokenCount?: number; candidatesTokenCount?: number; cachedContentTokenCount?: number }, model?: string): { model: string; inputTokens: number | undefined; outputTokens: number | undefined; cachedTokens: number | undefined }`
    *   `TranslationState` interface

*   Implement `createTranslationState` to initialize a `TranslationState` with:
    *   `streamStartEmitted` set to `false`
    *   `model` set to `undefined`
    *   `eventCounter` set to `0`
    *   `pendingToolNames` as an empty `Map`
    *   `streamId` as a provided value or auto-generated UUID

*   Implement `translateEvent` to convert `ServerGeminiStreamEvent` into `AgentEvent` objects:
    *   Return event arrays based on event type, updating `state` accordingly.
    *   For `Content` events, handle `streamStartEmitted` flag and format message content.
    *   For `Thought` events, include `_meta.subject`.
    *   For `ToolCallRequest` and `ToolCallResponse`, manage `pendingToolNames` and format content.
    *   For `Error` events, use `mapHttpToGrpcStatus` for structured errors and set status to 'INTERNAL' for `Error` instances.
    *   Handle `ModelInfo`, `AgentExecutionStopped`, `AgentExecutionBlocked`, `LoopDetected`, `MaxSessionTurns`, `Finished`, `Citation`, `UserCancelled`, `ContextWindowWillOverflow`, `InvalidStream`, `Retry`, `ChatCompressed`, and `ToolCallConfirmation` events as specified.

*   Implement `mapFinishReason` to map `FinishReason` values to `StreamEndReason` strings.

*   Implement `mapHttpToGrpcStatus` to convert HTTP status codes to gRPC-style status strings.

*   Implement `mapError` to convert various error forms into a structured error object.

*   Implement `mapUsage` to map usage metadata fields to a usage object.

*   Update `AgentEvent` type in `packages/core/src/agent/types.ts` to a discriminated union for automatic type narrowing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.