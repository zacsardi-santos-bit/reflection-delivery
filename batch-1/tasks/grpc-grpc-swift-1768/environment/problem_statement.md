## Description

The gRPC HTTP/2 transport layer is missing two foundational building blocks needed for encoding outbound messages. Without them, it's impossible to implement the message-encoding pipeline that formats data for transmission over an HTTP/2 connection.

## Expected Behavior

### Queue utility

A FIFO queue is needed that avoids a heap allocation in the common case where only a single element is held at a time. When more than one element is present simultaneously the queue may fall back to a heap-backed container. The queue must:

- Report whether it is empty and how many elements it holds.
- Expose Collection-compatible index navigation so callers can iterate or subscript into it.
- Allow elements to be appended to the back and popped from the front.
- Continue operating correctly after the internal backing has transitioned from single-element to multi-element mode and back.

### Message framer

A message framer is needed that accepts raw byte arrays (with an indication of whether each should be compressed), queues them internally, and on demand produces a single output buffer containing all pending messages formatted per the gRPC wire protocol. Specifically:

- Each message must be prefixed with a 1-byte compression indicator followed by a 4-byte message length.
- Multiple pending messages must be coalesced into one output buffer per call.
- Once all pending messages have been returned, the framer must signal that there is nothing left to deliver.

## Why This Matters

These two components are the lowest-level building blocks for sending gRPC messages over HTTP/2. Without them the rest of the encoding pipeline cannot be assembled.
