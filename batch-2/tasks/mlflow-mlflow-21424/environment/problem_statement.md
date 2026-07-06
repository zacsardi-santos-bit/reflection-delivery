## Description

The tracing system needs a more robust and flexible destination management layer that supports multiple priority levels when resolving where to export traces. Currently, the Unity Catalog span table name is resolved through a static helper each time a span is processed, and the destination registry has limited support for scoping destinations to specific experiments or concurrent execution contexts.

Additionally, the tracing configuration API needs to be updated to handle the distinction between two Unity Catalog destination types: the older schema-level type should emit a deprecation warning when passed to the configuration API, while the newer table-prefix type should be explicitly rejected via that API with an informative error. The same rejection logic should apply when the table-prefix format is specified through the environment variable.

## Expected Behavior

- The destination registry should support four priority levels when resolving the active destination: per-task/thread local override, global setting, per-experiment cached default, and environment variable fallback.
- Destinations can be cached per experiment, and when the active experiment changes the cached value is preserved but bypassed in favor of the environment variable.
- If looking up the active experiment fails transiently, the system should fall back to the cached per-experiment destination rather than crashing.
- Passing the older schema-level Unity Catalog destination type to the tracing configuration API should produce a clear deprecation warning.
- Passing the newer table-prefix Unity Catalog destination type to the tracing configuration API should raise an error.
- Specifying a three-part dot-separated path in the tracing destination environment variable should be rejected with a clear error message.
- The span processor should read its destination directly from the central registry rather than via a separate helper function.

## Why This Matters

This change makes it possible to scope trace destinations to individual experiments or concurrent tasks without interfering with other contexts, and provides clear guidance when deprecated or unsupported destination formats are used.
