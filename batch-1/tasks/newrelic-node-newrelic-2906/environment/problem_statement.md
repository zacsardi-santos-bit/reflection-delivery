## Description

The New Relic Node.js agent needs a bridge that translates spans created by OpenTelemetry instrumentation into New Relic's transaction segment model. Right now, when applications use OpenTelemetry APIs to create spans, those spans are invisible to New Relic — they don't appear in transaction traces, and no metrics are generated for them.

## Expected Behavior

- A new feature flag should gate the entire bridge so that teams can opt in when ready.
- When the bridge is enabled, spans started via the OpenTelemetry tracing API should be automatically captured and translated into New Relic transaction segments with the correct name, parent-child relationship, and duration.
- Internal spans should generate custom metrics under the transaction, and HTTP outbound spans should produce proper external call metrics (e.g. by host and method).
- When the bridge is disabled, a clear warning should be logged explaining why initialization was skipped.
- Successful initialization of the bridge should be tracked via a supportability metric so adoption can be measured.
- Context values passed through the OpenTelemetry context API should correctly propagate New Relic transaction and segment state when special synthesis metadata is present.

## Why This Matters

Teams that instrument their Node.js services using OpenTelemetry libraries should be able to see that instrumentation in New Relic without duplicating instrumentation work. This bridge allows both instrumentation approaches to coexist and ensures that OpenTelemetry spans contribute to transaction traces and metrics in the New Relic UI.
