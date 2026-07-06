## Description

When LangChain messages are serialized (for example, to store them in a database, log them, or pass them through a service boundary), they are written in an internal wire format that includes version markers, type metadata, and constructor arguments. When these serialized messages are later fed back to the message conversion utility, the conversion currently fails — it doesn't recognize the serialized format and can't reconstruct the correct message type.

## Expected Behavior

- The message conversion utility should recognize the standard internal serialization envelope format and reconstruct the appropriate message type (human, AI, system, tool, or function messages) using the provided constructor arguments.
- All fields should be preserved correctly during reconstruction, including tool calls on AI messages, artifact payloads on tool messages, and message IDs.
- Streaming chunk messages stored in the serialized format should be converted to their corresponding finalized message type.
- Unrecognized class names in the serialized format should fail with the same error as other unsupported input formats.
- Partially-formed dicts that resemble the serialized format but are missing required fields should fall through to the existing error path unchanged.
- Existing plain dict inputs (with a role or type field) must continue to work as before.

## Why This Matters

This is a common scenario in applications that persist conversation history: messages are serialized when saved and then must be deserialized when loaded back. Without this support, developers must manually convert the serialized format before passing messages to conversion utilities, adding friction and potential for bugs.
