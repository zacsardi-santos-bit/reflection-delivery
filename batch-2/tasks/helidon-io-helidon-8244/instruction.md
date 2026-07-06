Fix the OpenTelemetry tracing implementation to ensure setting baggage on an active span does not corrupt the span's scope management. Ensure that activating a span and setting baggage does not create additional unclosed context scopes, and that the context is properly restored after the scope closes.

*   Implement the `activate()` method in `OpenTelemetrySpan` to make the span the current span.
    *   Ensure `Span.current()` returns an `Optional` containing the activated span with the correct span ID.
    *   Return a `Scope` that, when closed, restores the prior context.

*   Implement the `baggage(String key, String value)` method in `OpenTelemetrySpan` to store baggage without altering the context scope.
    *   Ensure this method does not call `makeCurrent()` or create additional context scopes.

*   Ensure the `Scope` returned by `activate()` in `OpenTelemetrySpan` closes both the span scope and any associated baggage scope correctly.
    *   Ensure context is fully restored to its pre-activation state upon closing.

*   Update `OpenTelemetryScope` to manage both the span's and baggage's OTel context scopes.
    *   Implement the `close()` method to close both scopes in the correct order.

*   Implement `MutableOpenTelemetryBaggage` to manage baggage key-value pairs for a span.
    *   Implement `baggage(String key, String value)` to add or update entries without affecting the OTel context.
    *   Implement `makeCurrent()` to allow baggage activation as part of span activation.

*   Ensure that when the real OpenTelemetry SDK is configured, spans created via `Tracer.global()` and `spanBuilder` have non-no-op span IDs.
    *   Verify `span.context().spanId()` does not contain '00000000'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.