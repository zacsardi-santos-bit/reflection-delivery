## Description

There is a bug in the OpenTelemetry tracing implementation where setting baggage on a span that is currently active corrupts the span's scope management. After baggage is set, the context stack becomes disrupted, so when the scope is closed, the previously active context is not properly restored.

## Expected Behavior

- When a span is activated, it becomes the current span for the duration of the scope.
- Setting baggage on a span during activation should be a pure data operation — it must not create any additional context scope or affect which span is currently active.
- After the scope closes (whether or not baggage was set), the context must revert to exactly what it was before the span was activated. If no span was active before, the current span should revert to the default no-op span (identifiable by an all-zero span ID).

## What Currently Happens

When baggage is set on an active span, an internal context scope is created and left unclosed. When the original scope is later closed, it does not properly undo this extra context, so the "current span" after the scope exits does not match what was active before the span was activated.

## Why This Matters

Any code that sets baggage on an active span and then relies on span context being correctly restored afterward (for example, in nested span hierarchies or middleware chains) will silently get the wrong active span after the scope exits. This is a correctness issue that can cause spans to be attributed to the wrong traces or for context to leak across requests.
