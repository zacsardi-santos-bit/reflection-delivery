## Description

The TypeScript SDK is missing foundational entity types for working with traces. Currently there is no way to represent where a trace is stored, what lifecycle state it is in, or the metadata associated with it (such as its identifier, timestamps, and request/response previews). There is also no top-level object that ties together trace metadata and span data into a single structure that can be serialized and deserialized.

## Expected Behavior

- There should be a type that describes the possible states a trace can be in (e.g., in-progress, completed successfully, errored, or unspecified), including a utility that converts existing OpenTelemetry status codes into these states.
- There should be a type that represents where a trace is stored — for example, in an experiment or in an inference table — along with a convenience function for constructing a location from an experiment identifier.
- There should be a class that holds all trace metadata, including its ID, location, timestamps, request/response previews, tags, and metadata key-value pairs.
- There should be a class that holds the collection of spans associated with a trace.
- There should be a top-level trace class that combines the metadata and span data, with the ability to serialize the entire trace to a plain JSON object and reconstruct it from that JSON (round-trip serialization).

## Why This Matters

Without these types, developers using the TypeScript SDK cannot construct, inspect, or exchange trace data with the MLflow API. These are the core building blocks that all other trace-related functionality depends on.
