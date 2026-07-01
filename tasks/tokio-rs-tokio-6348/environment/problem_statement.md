## Description

When using async message-passing channels, the receiver side has no way to inspect the channel's current state without actually consuming messages. There is no way to ask "is this channel closed?", "are there any messages waiting?", or "how many messages are currently buffered?" — all of which are common needs when writing monitoring logic, implementing back-pressure strategies, or debugging channel behavior.

## Expected Behavior

The receiver types for both bounded and unbounded channels should expose three new inspection methods:

- A method to check whether the channel is closed. A channel is considered closed either when the receiver explicitly closes it, or when all sending ends have been dropped. This should return the correct value regardless of whether messages remain in the buffer, and should treat outstanding reserved permits as keeping the channel open. Dropping all strong senders while only weak senders remain should still mark the channel as closed.
- A method to check whether there are currently any messages in the channel buffer. This should return true when empty and false when at least one message is waiting, and remain accurate after partial or full consumption.
- A method to return the exact count of currently buffered messages. This should count only actual messages, not any internal bookkeeping entries written when the channel closes. The count should increment with each send and decrement with each receive, remaining accurate regardless of whether the receiver has been explicitly closed or senders have been dropped.

## Why This Matters

Without these methods, developers must resort to workarounds like consuming and re-queuing messages just to check channel state, or maintaining their own external state tracking. Adding these introspection methods makes it straightforward to monitor channel health and message load without disturbing the message queue.
