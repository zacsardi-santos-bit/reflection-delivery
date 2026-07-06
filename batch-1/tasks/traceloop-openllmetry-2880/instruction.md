Implement support for emitting prompt and response data as structured log events in the AlephAlpha OpenTelemetry instrumentation. Ensure the system can switch between the legacy span-attribute approach and the new event-based model using a flag. Maintain backward compatibility by defaulting to the legacy mode.

*   Update the `AlephAlphaInstrumentor` class:
    *   Accept a `use_legacy_attributes` constructor parameter, defaulting to `True`.
    *   When `use_legacy_attributes=True`, emit no log events and use only span attributes.
    *   Implement the `instrument()` method to accept an `event_logger_provider` keyword argument for routing log-based events.

*   Implement log event emission:
    *   When `use_legacy_attributes=False` and an `event_logger_provider` is provided:
        *   Emit two log events per completion: 
            *   User message event with event name 'gen_ai.user.message'.
            *   Choice/response event with event name 'gen_ai.choice'.
    *   Set event attributes:
        *   `EventAttributes.EVENT_NAME` to the event name string.
        *   `GenAIAttributes.GEN_AI_SYSTEM` to 'alephalpha'.

*   Manage content tracing:
    *   Export the `TRACELOOP_TRACE_CONTENT` constant from the `opentelemetry.instrumentation.alephalpha` package.
    *   When content tracing is enabled (`TRACELOOP_TRACE_CONTENT='True'`):
        *   User message event body must include a dict with key 'content' containing a list of prompt item dicts.
        *   Choice event body must include a dict with 'index', 'finish_reason', and 'message' keys, where 'message' contains a 'content' key with the completion text.
    *   When content tracing is disabled (`TRACELOOP_TRACE_CONTENT='False'`):
        *   User message event body must be falsy (None or empty).
        *   Choice event body must include a dict with 'index' and 'finish_reason' keys, but 'message' must be an empty dict.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.