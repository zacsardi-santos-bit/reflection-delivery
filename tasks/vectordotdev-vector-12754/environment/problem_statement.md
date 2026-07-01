## Description

The codecs library currently lacks a proper text encoding format. Many sinks in the system support a "text" encoding mode that is supposed to output the plain message content of a log event or the human-readable string representation of a metric. Right now, this behavior is being approximated by reusing the raw-message encoder as a stand-in, but there is no dedicated, first-class text serializer type.

As a result, the codecs crate fails to compile when any code tries to reference a text encoding format by its proper type. This compilation failure causes all tests in the package to fail.

## Expected Behavior

- A new text serialization format should be available as a distinct encoder type in the encoding format module.
- When serializing a log event, the encoder should extract the event's message field and write its bytes to the output buffer.
- When serializing a metric, the encoder should convert it to its human-readable string representation and write those bytes to the output buffer.
- The new type should be exported from the codecs library alongside the existing format types so downstream code can use it by name.
- The encoding configuration and serializer enums should include a corresponding variant for the new text format.

## Why This Matters

Several sinks already expose a "text" encoding option in their configuration. Without a dedicated text encoder type, these sinks cannot properly migrate to the new encoding framework and the entire codecs package fails to compile, blocking development and testing.
