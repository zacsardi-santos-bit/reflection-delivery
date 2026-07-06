## Description

MLflow's tracing system currently only supports creating traces and spans through high-level decorators and context managers. There is no way for developers to manually start a trace, add child spans one at a time, and finalize everything explicitly — a pattern that is necessary when integrating with external callback-based tracing frameworks (such as LangChain callbacks). Additionally, the internal component that accumulates spans needs to be promoted into a proper trace manager that exposes helper utilities for looking up spans and root span IDs.

A related issue is that span timestamps are currently stored and compared in nanoseconds, but MLflow's other time-related fields use milliseconds or microseconds. This inconsistency causes assertions that check minimum elapsed time to fail when the wrong unit is assumed.

## Expected Behavior

- Developers should be able to use the MLflow client directly to start a trace, create one or more child spans, and end them individually before finalizing the trace.
- Attempting to start a child span without supplying a parent span identifier should raise a clear error.
- If the trace is finalized before some child spans are explicitly ended, those spans should still be included in the exported trace data, with their status marked as unset and their end time absent.
- The internal trace manager should be a singleton that provides methods to look up spans by ID and retrieve the root span ID for a given trace.
- All span timestamps must be represented in microseconds, not nanoseconds.
- Mixing the high-level context-manager API with the imperative client API within the same trace should work correctly — all spans should be linked and visible in a single trace.

## Why This Matters

Advanced integrations with frameworks like LangChain use callbacks that do not fit neatly into Python context managers. Developers working in those environments need a lower-level API that gives them explicit control over when spans are started and ended, while still producing well-formed MLflow traces that can be searched and visualized.
