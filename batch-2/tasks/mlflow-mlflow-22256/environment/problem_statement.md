## Description

MLflow currently supports storing and retrieving trace data only in a JSON text format. There is no support for persisting traces in a compact, binary archival format based on the OpenTelemetry protocol. This makes long-term storage of large trace datasets less efficient than it could be, and prevents interoperability with systems that consume the standard OpenTelemetry binary wire format.

## Expected Behavior

- A new serialization utility should be introduced that can convert a list of trace spans into a binary protobuf representation using the OpenTelemetry Protocol wire format, and back.
- The serialization utility should validate input before writing: it should reject an empty span list, reject spans that belong to more than one trace, and reject spans that carry inconsistent resource metadata. However, resources that have identical attributes in different key order should be treated as equivalent.
- The deserialization utility should validate the binary payload: it should reject empty or unparseable bytes, reject payloads with multiple resource-span groups or multiple scope-span groups, reject payloads with spans from different traces, and reject payloads that contain no spans at all.
- The filename used for storing the binary archive should be a fixed constant defined within the module.
- Local filesystem and S3 artifact repositories should each gain three new operations: upload raw protobuf bytes, upload a trace data object (accepting either the native object or its JSON representation), and download the archived trace data back as a trace data object.
- On Databricks-backed artifact repositories, these three archival operations are not yet supported. Each should raise an informative error indicating that archive-repo operations are not yet supported, rather than silently failing or producing incorrect behavior.

## Why This Matters

Storing traces in binary protobuf format is more space-efficient and enables richer round-trip fidelity (e.g. preserving resource metadata, span events, and status codes) compared to the existing JSON format. Having a well-validated serialization layer also ensures that corrupt or malformed archive files are caught early with clear error messages rather than causing confusing downstream failures.
