Implement a session class, `LocalSubagentSession`, that connects locally-defined subagents to the standard event-based agent infrastructure. This class should wrap a local executor and translate its activity callbacks into structured agent events. Ensure the session supports subscribing to events, sending input, retrieving the final result, and canceling execution.

*   Constructor Requirements:
    *   Accept four parameters: `LocalAgentDefinition`, `AgentLoopContext`, `MessageBus`, and an optional `rawActivityCallback`.
    *   Ensure `MessageBus` is included for API parity.

*   Method: `send(payload: AgentSend): Promise<{ streamId: string | null }>`
    *   For update payloads with a config object, buffer the config and return `{ streamId: null }`.
    *   For message payloads, merge buffered config with the message's query text, pass to the executor, and return `{ streamId: string }`.
    *   Omit the 'query' key if the message text is empty.
    *   Clear buffered config after each message send.
    *   Throw an error with 'cannot be called while a stream is active' if a message payload is sent while a stream is active.

*   Method: `getResult(): Promise<OutputObject>`
    *   Resolve with the executor's `OutputObject` on successful execution.
    *   Reject with the executor's error if execution fails.
    *   Reject with 'No active or completed stream' if called before any send.
    *   Return the result of the most recent stream after multiple sends.

*   Method: `abort(): Promise<void>`
    *   Cancel the currently executing stream.
    *   Resolve `getResult()` with `{ result: '', terminate_reason: 'ABORTED' }` after abort.
    *   Emit an `agent_end` event with reason 'aborted'.

*   Method: `subscribe(callback: (event: AgentEvent) => void): Unsubscribe`
    *   Register an event listener and return an Unsubscribe function to stop event delivery.
    *   Support multiple subscribers receiving the same events.

*   Property: `events: readonly AgentEvent[]`
    *   Accumulate all emitted `AgentEvents`.
    *   Ensure the first event of each stream is `agent_start` and the last is `agent_end`.

*   Event and Lifecycle Management:
    *   Emit `agent_start` and `agent_end` events exactly once per stream.
    *   Ensure all events in a stream share the same `streamId`.
    *   Map executor terminate modes to `agent_end` reasons appropriately.
    *   Translate `SubagentActivityEvent` types to `AgentEvent` types as specified.
    *   Invoke `rawActivityCallback` with raw events before translation if provided.

*   Ensure sequential interactions after a stream completes produce new streams with distinct identifiers.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.