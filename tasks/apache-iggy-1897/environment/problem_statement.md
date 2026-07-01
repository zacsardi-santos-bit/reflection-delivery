## Description

The Go client library for Iggy uses an outdated message format that no longer matches the server's wire protocol. The current message representation is a simple structure with a UUID, a payload byte slice, and a map of typed header key-value pairs. This does not align with the actual binary protocol the server expects, which defines a fixed-size binary message header containing fields for checksum, offset, timestamps, and length metadata, with user-defined headers stored as pre-serialized bytes rather than a structured map.

As a result, messages produced by the Go client cannot be correctly received or interpreted by the server (and vice versa). Similarly, the way consumer identifiers are encoded in fetch requests does not match the updated server protocol, which now expects an identifier format that supports both numeric and string IDs.

## Expected Behavior

- Messages should use a new structured representation with a fixed-size binary header (containing checksum, UUID, offset, timestamps, and length fields), a payload field, and a pre-serialized user headers byte field.
- Helper constructors should be available for creating messages with or without user-defined headers, accepting an explicit UUID and payload.
- The binary serialization of send-messages requests should include a metadata-length prefix, a per-message index section, and serialize each message as its binary header followed by payload and user header bytes.
- The binary serialization of fetch-messages requests should encode the consumer identifier using the full identifier format (kind, length, value), not as a bare integer.
- The response deserialization for fetched messages should parse the new fixed-size binary header format.

## Why This Matters

Without these changes, the Go client cannot correctly exchange messages with an Iggy server running the updated protocol. Producers will send malformed data and consumers will fail to parse server responses, making the client unusable for the current server version.
