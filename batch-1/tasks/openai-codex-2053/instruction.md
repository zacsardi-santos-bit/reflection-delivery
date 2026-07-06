Implement a mechanism to handle API errors during streaming conversation turns, ensuring the session is recoverable and can continue processing new messages. Update the test support helper function to facilitate event waiting with a timeout.

*   Emit an error event followed by a task completion event when an API error occurs during a streaming conversation turn.
    *   Ensure this happens even if the streaming request fails.
*   Release the session/task state completely after emitting both events for a failed turn.
    *   Allow subsequent user inputs to be accepted and processed normally.
*   Handle subsequent user inputs as new turns, ensuring they complete successfully with their own task completion event.
*   Update the test support helper function in `codex-rs/core/tests/common/lib.rs`:
    *   Implement `wait_for_event_with_timeout<F>(codex: &codex_core::Codex, predicate: F, wait_time: tokio::time::Duration) -> codex_core::protocol::EventMsg` where `F: FnMut(&codex_core::protocol::EventMsg) -> bool`.
    *   Ensure `wait_for_event` delegates to `wait_for_event_with_timeout` with a default timeout of 1 second.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.