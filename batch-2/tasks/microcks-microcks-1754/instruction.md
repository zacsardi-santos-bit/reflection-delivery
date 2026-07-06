Implement a real-time trace streaming feature in Microcks to notify clients immediately when new trace data arrives. Create a subscription manager to allow clients to register for filtered live streams of trace events, supporting flexible matching criteria.

*   Create a `TraceEvent` class in `io.github.microcks.event` package:
    *   Implement as a record with fields: `traceId`, `service`, `operation`, `clientAddress` (all `String`).
    *   Provide accessor methods: `traceId()`, `service()`, `operation()`, `clientAddress()`.

*   Modify `SpanStorageService`:
    *   Replace the no-arg constructor with one accepting an `ApplicationEventPublisher`.
    *   On receiving a root span in `storeSpan(ReadableSpan span)`, publish a `TraceEvent` using `ApplicationEventPublisher`.
        *   Extract `service` from 'service.name', `operation` from 'operation.name', and `traceId` from the span's trace context.
        *   Set `service` and `operation` to null if attributes are absent.
    *   Do not publish events for non-root spans.

*   Implement `SpanFilterUtil` in `io.github.microcks.util.tracing` package:
    *   `matchesWildcard(String pattern, String value)`: 
        *   Return true if both arguments are null.
        *   Return false if only one is null.
        *   Return true for any non-null value if pattern is '*'.
        *   Match value against pattern as a full regex; fall back to exact string equality if invalid.
    *   `extractTraceEvent(String traceId, List<ReadableSpan> spans)`: 
        *   Extract 'service.name', 'operation.name', and 'client.address' attributes.
        *   Return a `TraceEvent` with extracted values (null if absent).
    *   `matchesTraceEvent(TraceEvent event, String service, String operation, String clientAddress)`:
        *   Use `matchesWildcard` to check if all three event fields match their respective patterns.

*   Develop `TraceSubscriptionManager` in `io.github.microcks.web` package:
    *   Constructor accepts a `SpanStorageService`.
    *   `subscribe(SseEmitter emitter, String serviceName, String operationName, String clientAddress)`:
        *   Register emitter with filter criteria.
        *   Immediately send a heartbeat SSE event to the emitter.
    *   `onTraceUpdated(TraceEvent event)`:
        *   Retrieve spans using `SpanStorageService.getSpansForTrace()`.
        *   If spans are empty, do not send SSE events.
        *   For matching subscribers (via `SpanFilterUtil.matchesTraceEvent`), send SSE events.
        *   On `IOException` during send, call `complete()` on the emitter.
    *   `sendHeartbeats()`:
        *   Send a heartbeat SSE event to all registered emitters.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.