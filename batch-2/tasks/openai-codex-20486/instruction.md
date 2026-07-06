I'm running into an issue with the thread-read endpoint when I ask it to include the conversation turns in the response.

*   The thread-read request params struct must include a boolean field named `include_turns` that, when set to true, causes the response to populate the thread's turns with conversation history from the thread store.

*   When `include_turns` is true, the `thread/read` endpoint must return conversation turns for loaded threads even if those threads do not have a file-based rollout path, as long as the thread is not ephemeral.

*   When `include_turns` is true for a loaded non-ephemeral thread, turns must be sourced from the thread store history (including in-memory stores), and the resulting `ThreadReadResponse` must have `thread.turns` populated with those items.

*   User message texts from the thread store must appear in `thread.turns` and be accessible when processing the response, confirming that history items appended to the store before the read request are reflected in the turns.


*   Interface details: Type: Struct
Name: ThreadReadParams
Location: codex-rs/app-server-protocol/src/protocol/v2.rs
Description: Parameters for the `thread/read` request. Must include a `thread_id: String` field and an `include_turns: bool` field. When `include_turns` is true, the response must populate `thread.turns` with conversation history from the thread store.
Signature: ThreadReadParams { thread_id: String, include_turns: bool }

Type: Struct
Name: ThreadReadResponse
Location: codex-rs/app-server-protocol/src/protocol/v2.rs
Description: Response for the `thread/read` request. Must contain a `thread: Thread` field where `Thread` has a `turns` field populated with conversation turns when `include_turns` was set to true in the request.
Signature: ThreadReadResponse { thread: Thread, .. }

Type: Handler (internal function)
Name: load_live_thread_view (or equivalent thread/read handler logic)
Location: codex-rs/app-server/src/ (app-server message processor)
Description: The handler that processes thread/read requests for loaded threads must be updated so that `include_turns: true` checks whether the thread is ephemeral (not whether a rollout path is present). For non-ephemeral loaded threads, turns must be sourced from the thread store via `load_history` rather than from the rollout file path.
Signature: load_live_thread_view(thread_id: ThreadId, include_turns: bool, loaded_thread: &CodexThread, persisted_thread: Option<Thread>) -> Result<Thread, ThreadReadViewError>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.