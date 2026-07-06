Implement the necessary functionality to ensure that server-sent events (SSE) properly handle client disconnections and broadcaster operations. Ensure that close callbacks are invoked when clients disconnect and that broadcasts skip closed connections without error.

*   Implement `SseBroadcasterImpl.close()`:
    *   Call `close()` on every registered `SseEventSinkImpl` sink.
    *   Ensure all sinks are closed when the broadcaster is closed.

*   Implement `SseBroadcasterImpl.broadcast(OutboundSseEvent event)`:
    *   Skip any sinks that have already been closed.
    *   Avoid invoking `send()` on closed sinks to prevent error callbacks.

*   Implement `SseEventSinkImpl.sendInitialResponse(ServerHttpResponse response)`:
    *   Register a close handler on the HTTP response using `response.addCloseHandler(Runnable)`.
    *   Ensure the runnable closes the sink, triggering the broadcaster's close logic.

*   Implement `SseBroadcasterImpl.fireClose(SseEventSinkImpl sseEventSink)`:
    *   Invoke all registered `onClose` callbacks when a sink is closed.
    *   Remove the closed sink from the internal sinks collection if the broadcaster is not closed.

*   Implement `SseEventSinkImpl.close()`:
    *   Make the method idempotent; if the sink is already closed, return immediately.
    *   Call `response.end()` and `context.close()` only if the HTTP response is not already closed.

*   Ensure that when an SSE client disconnects:
    *   The broadcaster's registered `onClose` callbacks are invoked on the server side.

*   After a sink is closed:
    *   Subsequent broadcasts must not trigger the broadcaster's `onError` callbacks.
    *   Closed sinks must be silently skipped during broadcasts.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.