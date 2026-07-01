## Description

The AlephAlpha instrumentation currently records prompt and response data exclusively as span attributes. However, the OpenTelemetry ecosystem has been moving toward using structured log-based events for capturing LLM inputs and outputs, following newer semantic conventions. We need to add support for this event-based model alongside the existing attribute-based approach.

## Expected Behavior

- The instrumentor should support a flag that switches between the legacy attribute-based mode and the new event-based mode.
- When the new event-based mode is active, each completion should emit two structured log events: one capturing the user's prompt input and one capturing the model's response.
- The events should carry the appropriate semantic attributes identifying the event name and the AI system.
- The events should respect the existing content-tracing setting: when content tracing is enabled, the full prompt and response content should be included in the event bodies; when disabled, the events should still be emitted but with no content in their bodies.
- The legacy attribute-based mode should remain the default and should produce no log events, ensuring backward compatibility.
- The instrumentor should accept an event logger provider so events can be routed to the appropriate OpenTelemetry pipeline.

## Why This Matters

Teams adopting modern OpenTelemetry observability practices need LLM instrumentation libraries to emit data in the standardized event format. Without this, users of the AlephAlpha instrumentation cannot take advantage of event-based log pipelines for their AI observability workflows.
