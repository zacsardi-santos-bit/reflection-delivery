Implement a mock agent session in the core package to facilitate testing without a real backend. Ensure it can queue events, simulate streaming, and handle session state changes automatically. Provide error handling for unsupported actions and maintain a complete event history for assertions.

*   Implement `MockAgentSession` class in `packages/core/src/agent/mock.ts`:
    *   Must implement the `AgentSession` interface from `packages/core/src/agent/types.ts`.
    *   Constructor: `constructor(initialEvents?: AgentEvent[])`.

*   Implement `pushResponse(events: MockAgentEvent[], options?: PushResponseOptions): void`:
    *   Queue partial event objects for the next `send()` call.
    *   Use `PushResponseOptions` to control stream closure with `keepOpen`.

*   Implement `send(payload: AgentSend): Promise<{ streamId: string }>`:
    *   Return a unique `streamId` for each call.
    *   Automatically prepend a `stream_start` event if absent.
    *   Inject events based on payload type:
        *   'message': Insert `message` event with role 'user'.
        *   'update': Insert `session_update` event and update session properties.
        *   'elicitations': Insert `elicitation_response` events for each entry.
        *   'action': Throw error 'Actions not supported in MockAgentSession: {type}'.
    *   Append `stream_end` event unless `keepOpen` is true or already present.

*   Implement `stream(options?: { streamId?: string; eventId?: string }): AsyncIterableIterator<AgentEvent>`:
    *   Stream events from the latest or specified stream.
    *   Throw errors for non-existent `streamId` or `eventId`.
    *   Suspend if stream is active and no more events are queued.

*   Implement `abort(): Promise<void>`:
    *   Emit `stream_end` event with reason 'aborted' for suspended streams.

*   Implement `pushToStream(streamId: string, events: MockAgentEvent[], options?: { close?: boolean }): void`:
    *   Append events to the active stream and resume waiting consumers.
    *   Append `stream_end` event with reason 'completed' if `close` is true and no `stream_end` is present.

*   Implement `get events(): AgentEvent[]`:
    *   Return all events yielded across all streams, maintaining order.

*   Ensure all methods and properties are correctly defined and accessible as per the interface requirements.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.