## Description

The current gRPC message deframing logic is implemented as a single component tightly coupled to the channel pipeline infrastructure. This makes it hard to use the deframing functionality independently outside of that pipeline. The existing component also bundles together two concerns: low-level gRPC wire format parsing, and higher-level buffer management.

We should split this into two separate components:

1. A low-level decoder that handles the gRPC wire format and integrates with the channel pipeline infrastructure as a single-step byte-to-message decoder. This component owns the parsing logic: reading the compression flag, enforcing payload size limits, and optionally decompressing the payload.

2. A higher-level deframer that wraps the low-level decoder and exposes a simple push/pull API: callers append raw bytes as they arrive, and then ask for the next decoded message. If a complete message is not yet available, the deframer returns nothing. This makes the deframer usable outside of the channel pipeline.

## Expected Behavior

- The low-level decoder enforces the configured maximum payload size as soon as the message header is read, before consuming the payload bytes.
- If a message is flagged as compressed but no decompressor is configured, an appropriate error is returned.
- If the decompressed size of a message exceeds the configured limit, an appropriate error is returned.
- The higher-level deframer buffers bytes internally. When new bytes are appended after the caller has read some messages, already-processed bytes in the buffer are discarded to avoid unbounded memory growth.
- The deframer correctly handles messages being delivered as partial byte sequences (drip-fed).

## Why This Matters

This separation of concerns makes both components easier to test and use independently. It also improves memory efficiency by ensuring that already-decoded bytes are cleaned up from the internal buffer when new bytes arrive.
