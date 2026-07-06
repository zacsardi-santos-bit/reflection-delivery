Implement three new inspection methods for both bounded and unbounded channel receiver types to allow checking the channel's state without consuming messages. These methods will enable you to determine if the channel is closed, check if the buffer is empty, and get the count of buffered messages.

*   Implement `Receiver::is_closed(&self) -> bool` in `tokio/src/sync/mpsc/bounded.rs`:
    *   Return `true` if `Receiver::close()` has been called, regardless of remaining senders.
    *   Return `true` if all strong senders have been dropped, even if messages are buffered.
    *   Return `false` if at least one strong sender exists, even if the buffer is full.
    *   Return `false` if an outstanding owned permit exists, even without direct sender references.
    *   Return `true` if all strong senders are dropped, even if weak sender references remain.

*   Implement `Receiver::is_empty(&self) -> bool` in `tokio/src/sync/mpsc/bounded.rs`:
    *   Return `true` if no messages are buffered, and `false` if one or more messages are present.
    *   Accurately reflect buffer state after partial or full message consumption, even after senders are dropped.

*   Implement `Receiver::len(&self) -> usize` in `tokio/src/sync/mpsc/bounded.rs`:
    *   Return the exact number of buffered messages, returning `0` for an empty channel.
    *   Do not count internal sentinel nodes written when all senders are dropped.
    *   Increment by 1 for each message sent and decrement by 1 for each message received, remaining accurate through close and drop operations.

*   Implement `UnboundedReceiver::is_closed(&self) -> bool` in `tokio/src/sync/mpsc/unbounded.rs`:
    *   Follow the same semantics as `Receiver::is_closed`.

*   Implement `UnboundedReceiver::is_empty(&self) -> bool` in `tokio/src/sync/mpsc/unbounded.rs`:
    *   Follow the same semantics as `Receiver::is_empty`.

*   Implement `UnboundedReceiver::len(&self) -> usize` in `tokio/src/sync/mpsc/unbounded.rs`:
    *   Follow the same semantics as `Receiver::len`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.