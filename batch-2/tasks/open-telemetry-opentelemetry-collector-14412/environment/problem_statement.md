## Description

The collector's telemetry resource configuration currently only supports a flat inline key-value map format where all values must be strings (or nil to remove an attribute). This is inflexible and does not accommodate richer attribute types such as booleans, integers, or floats, and mixes resource declaration with deletion semantics in an unstructured way. A new, structured declarative format needs to be introduced where resource attributes are expressed as a typed list, while still accepting the old format for backward compatibility.

Additionally, the telemetry providers (logger, metrics, tracing) currently create or manage their own resources internally, which makes it difficult to ensure consistent resource usage across all signals. These providers should instead require an explicit, pre-built resource to be injected through their settings, and must produce a clear, identifiable error if no resource is provided.

## Expected Behavior

- A new structured format for resource configuration must be supported, allowing attributes to be declared as a typed list with support for strings, booleans, integers of all sizes, and floating-point numbers.
- The old inline key-value format must continue to work for backward compatibility, but its use should produce a deprecation warning in the logs.
- Using the old and new formats simultaneously in the same configuration must be rejected with a clear error message.
- Certain unsupported configuration keys (specifically an attribute-list style key) must be rejected with a clear error message.
- The telemetry providers (logger, meter provider, tracer provider) must accept a pre-built resource through their settings and must return a clear error when no resource is supplied.
- Resource creation must be independent per configuration — no shared state or caching across different calls.

## Why This Matters

Operators configuring the collector's telemetry need flexibility to express resource attributes in a typed way, consistent with how the rest of the OpenTelemetry ecosystem handles resource attributes. The current string-only map format is a legacy design that should be deprecated in favor of a more expressive and type-safe configuration structure. Making the resource an explicit input to each telemetry provider also improves separation of concerns and makes misconfiguration immediately visible.
