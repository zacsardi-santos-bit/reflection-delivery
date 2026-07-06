## Migrate Groq Instrumentation to Current Generative AI Semantic Conventions

## Description

The Groq LLM instrumentation is currently using outdated telemetry attribute naming and span conventions. Spans are named with a legacy provider-specific format, message content is stored as flat string attributes, and several key attributes (streaming status, token counts) use old names that do not align with the current generative AI observability standard. This means observability dashboards and tooling that expect the standardized format cannot correctly interpret the telemetry produced by this library.

## Expected Behavior

- Chat spans should be named by operation and model (e.g., operation + space + model name), replacing the legacy provider-prefixed name.
- Every span should carry the provider name, operation name, and request model attributes as defined by the current standard.
- Input and output messages should be stored as structured JSON arrays, with each message having a role and a list of typed content parts (text, images, tool calls, tool responses). The old flat string attributes for prompt and completion content should no longer be used.
- The attribute names for streaming status and total token usage should be updated to the current standard names.
- Finish reasons should be recorded as a list attribute on each span (in non-legacy mode).
- Log events emitted during tracing should carry a provider name attribute rather than the legacy system attribute.
- The finish reason value used by the Groq API for tool invocations (a plural form) must be mapped to the singular form required by the standard.

## Why This Matters

This update ensures the Groq instrumentation produces telemetry that is compatible with the broader OpenTelemetry generative AI ecosystem. It also requires organizing the instrumentation logic into clearly separated modules — for span attribute utilities, event emission, event models, and shared helpers — so that each area can be maintained and tested independently.
