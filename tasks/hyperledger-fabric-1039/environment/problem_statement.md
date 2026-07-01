## Description

There is a bug in the gossip communication layer's stream reading logic. When a malformed or unparseable message is received from a remote peer, the system correctly records the error, but then continues processing in the loop instead of stopping. This causes undefined behavior — the loop continues running even after encountering an unrecoverable error condition.

As a consequence, the server also fails to properly close connections with peers that continuously send corrupted data. A remote peer sending invalid envelope data should trigger the server to forcibly terminate the stream, but currently the server may continue trying to process messages instead.

## Expected Behavior

- When the stream reader encounters a message that cannot be parsed, it should record the error and immediately stop reading further messages from the stream.
- No message should be forwarded downstream after an error in parsing.
- When a client repeatedly sends malformed data over an established connection, the server should forcibly close the stream, causing the client to receive a connection-closed error.

## Why This Matters

Without this fix, the reading loop can enter an inconsistent state after receiving bad data, potentially leaking goroutines or causing silent failures. Peers sending garbage data are not properly disconnected, which could be exploited to disrupt gossip communication or cause resource exhaustion.
