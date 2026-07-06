## Description

The judge adapter framework currently requires each adapter subclass to implement its own telemetry recording logic when invoking Databricks-backed models. This duplicates the same telemetry code across multiple adapters — every adapter that calls a Databricks model provider must independently handle success and failure telemetry, retry-safe error suppression, and token usage extraction. This is fragile and hard to maintain: if telemetry behavior needs to change, every adapter must be updated individually.

Additionally, the gateway-style adapter currently discards token usage information (prompt tokens, completion tokens) and request identifiers from the underlying model invocation, returning only the feedback result. This means downstream telemetry has no data to record even if it were properly wired up.

## Expected Behavior

- The base adapter class should centralize telemetry recording so all subclasses automatically get consistent behavior without implementing it themselves.
- When a Databricks-backed model is called and the invocation succeeds, usage metrics (token counts and request ID) should be recorded to the telemetry system automatically.
- When a Databricks-backed model is called and the invocation fails with a recognized error type, failure telemetry should be recorded automatically before re-raising the error.
- Telemetry failures must never disrupt the judge invocation — they should be silently suppressed.
- The gateway-style adapter should capture and forward token usage and request metadata from the underlying model response.
- The telemetry utility functions should live in a shared utilities module, not inside a specific adapter module.

## Why This Matters

Centralizing telemetry in the base class ensures that all current and future adapters get accurate, consistent usage tracking without any per-adapter boilerplate. It also corrects missing token data in the gateway adapter that would otherwise leave telemetry incomplete.
