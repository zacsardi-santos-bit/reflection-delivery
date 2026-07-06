## Description

The OpenTelemetry input plugin in Fluent Bit currently processes OTLP log payloads as flat individual records, silently discarding the hierarchical context that is an essential part of the OTLP data model. When logs arrive over OTLP, the resource-level attributes (which identify the originating service or system) and the scope-level metadata (which identifies the instrumentation library along with its name, version, and attributes) are never preserved in the output. This means any downstream consumer of Fluent Bit data loses all OTLP provenance information and cannot correlate log records back to their originating resource or instrumentation scope.

## Expected Behavior

- When OTLP logs are ingested, each resource-scope combination in the payload should produce a group header record in the output chunk that carries the resource attributes and scope metadata (name, version, and attributes)
- The group header should also include schema identification and integer indices identifying which resource and scope within the original payload the group belongs to
- Individual log records should follow the group header in the same chunk, containing the actual log message body
- The library output plugin should gain a new delivery mode that passes complete raw event chunks (including group headers) directly to callback functions, allowing consumers to inspect the full grouped output

## Why This Matters

Preserving OTLP context is critical for observability pipelines operating in multi-service environments. Without resource and scope metadata, operators cannot distinguish which service, library, or version produced a given log entry, defeating the purpose of structured OTLP instrumentation. The new chunk delivery mode for the library output plugin also enables integration tests and embedded consumers to validate the complete grouped structure rather than only individual records.
