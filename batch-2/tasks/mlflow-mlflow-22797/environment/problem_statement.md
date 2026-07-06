## Description

MLflow spans currently have no mechanism for expressing relationships to spans that exist in other traces. In distributed or multi-step systems, it is common for a span in one trace to be causally related to — or triggered by — a span in a completely different trace. Without a way to record these cross-trace connections, the relationship is lost.

This feature request adds support for attaching cross-trace links to MLflow spans. Each link should identify a target span by its trace ID and span ID, and optionally carry metadata attributes that describe the nature of the relationship. These links need to be:

- **Attachable to spans** while they are being recorded
- **Preserved through serialization** — survives conversion to and from a dictionary representation
- **Preserved through the OpenTelemetry wire format** — round-trips to and from the OTel proto representation without loss
- **Persisted in the tracking database** — stored and retrieved faithfully via the tracking store
- **Format-validated** — trace IDs and span IDs must match the expected formats; malformed identifiers should be rejected with a clear error message

Links should be supported for standard MLflow traces. Spans associated with the Unity Catalog v4 trace format should have an empty link list and should not attempt to populate links from the underlying OpenTelemetry data.

## Expected Behavior

- A new link entity type can be constructed with a target trace ID, target span ID, and optional attributes
- A live span can have links added to it; subsequent reads return the stored links
- Serializing a span to a dictionary includes a links field; deserializing reconstructs the links
- Converting a span to/from the OTel proto format preserves link data
- Storing and retrieving a span via the tracking store preserves all link fields
- Adding a link with a malformed trace ID or span ID raises an error

## Why This Matters

This enables richer observability for workflows where one trace depends on or was initiated by another, making it possible to trace causal chains and dependencies across trace boundaries.
