I'm working on adding support for a new kind of hook that fires before a user submits a prompt.

*   The `codex_app_server_protocol` crate must export a `HookEventName` enum that includes a `UserPromptSubmit` variant (in addition to any previously existing variants such as pre-tool-use).

*   The `codex_app_server_protocol` crate must export a `HookOutputEntryKind` enum that includes `Warning` and `Stop` variants.

*   The `codex_app_server_protocol` crate must export a `HookRunStatus` enum that includes `Running` and `Stopped` variants.

*   The `codex_app_server_protocol` crate must export `HookOutputEntry` (with `kind: HookOutputEntryKind` and `text: String` fields), `HookRunSummary`, `HookStartedNotification`, `HookCompletedNotification`, `HookHandlerType`, `HookExecutionMode`, and `HookScope` types.

*   The `HookRunSummary` struct must have the fields: `id: String`, `event_name: HookEventName`, `handler_type: HookHandlerType`, `execution_mode: HookExecutionMode`, `scope: HookScope`, `source_path: PathBuf`, `display_order: u32`, `status: HookRunStatus`, `status_message: Option<String>`, `started_at: i64`, `completed_at: Option<i64>`, `duration_ms: Option<u64>`, `entries: Vec<HookOutputEntry>`.

*   The chat widget must handle `ServerNotification::HookStarted` and `ServerNotification::HookCompleted` notifications by converting `codex_app_server_protocol` hook types directly to `codex_protocol::protocol` hook types (using dedicated conversion functions, not a JSON round-trip). Previously, failed conversions silently dropped the notification; the new approach must not silently drop valid hook notifications.

*   When a `HookStarted` notification is received for a `UserPromptSubmit` event with a status message, the chat widget must render a line in the form: `• Running UserPromptSubmit hook: <status_message>` (where `<status_message>` is the value of `status_message`).

*   When a `HookCompleted` notification is received for a `UserPromptSubmit` event with `HookRunStatus::Stopped` and output entries, the chat widget must render a block in the following form (each entry on its own line, indented by two spaces, prefixed with the lowercase kind name and a colon): `UserPromptSubmit hook (stopped)\n  warning: <text>\n  stop: <text>`.

*   The internal `event_survives_session_refresh` predicate in `ThreadEventStore` must be updated so that `ServerNotification::HookStarted` and `ServerNotification::HookCompleted` notifications survive a call to `rebase_buffer_after_session_refresh()`. Before this change, only `Request` events survived; after, hook notifications must also be preserved.

*   After calling `rebase_buffer_after_session_refresh()` on a `ThreadEventStore` that contains pushed `HookStarted` and `HookCompleted` notifications, the store's snapshot must still contain those notifications as `ThreadBufferedEvent::Notification` entries in the same order they were pushed.


*   Interface details: Type: Enum
Name: HookEventName
Location: codex-rs/app_server_protocol/src/lib.rs (or equivalent codex_app_server_protocol source)
Description: Enum identifying the type of hook event. Must include a `UserPromptSubmit` variant in addition to any existing variants (e.g. pre-tool-use).
Signature: enum HookEventName { UserPromptSubmit, /* ...other existing variants... */ }

Type: Enum
Name: HookOutputEntryKind
Location: codex-rs/app_server_protocol/src/lib.rs (or equivalent codex_app_server_protocol source)
Description: Enum for categorizing a hook output entry. Must include `Warning` and `Stop` variants.
Signature: enum HookOutputEntryKind { Warning, Stop, /* ...other existing variants... */ }

Type: Enum
Name: HookRunStatus
Location: codex-rs/app_server_protocol/src/lib.rs (or equivalent codex_app_server_protocol source)
Description: Enum for the run status of a hook. Must include `Running` and `Stopped` variants.
Signature: enum HookRunStatus { Running, Stopped, /* ...other existing variants... */ }

Type: Struct
Name: HookOutputEntry
Location: codex-rs/app_server_protocol/src/lib.rs (or equivalent codex_app_server_protocol source)
Description: Represents a single output entry produced by a hook run.
Signature: struct HookOutputEntry { kind: HookOutputEntryKind, text: String }

Type: Struct
Name: HookRunSummary
Location: codex-rs/app_server_protocol/src/lib.rs (or equivalent codex_app_server_protocol source)
Description: Full summary of a hook execution run.
Signature: struct HookRunSummary { id: String, event_name: HookEventName, handler_type: HookHandlerType, execution_mode: HookExecutionMode, scope: HookScope, source_path: PathBuf, display_order: u32, status: HookRunStatus, status_message: Option<String>, started_at: i64, completed_at: Option<i64>, duration_ms: Option<u64>, entries: Vec<HookOutputEntry> }

Type: Struct
Name: HookStartedNotification
Location: codex-rs/app_server_protocol/src/lib.rs (or equivalent codex_app_server_protocol source)
Description: Notification sent when a hook begins execution.
Signature: struct HookStartedNotification { thread_id: String, turn_id: Option<String>, run: HookRunSummary }

Type: Struct
Name: HookCompletedNotification
Location: codex-rs/app_server_protocol/src/lib.rs (or equivalent codex_app_server_protocol source)
Description: Notification sent when a hook finishes execution.
Signature: struct HookCompletedNotification { thread_id: String, turn_id: Option<String>, run: HookRunSummary }

Type: Function
Name: hook_output_entry_from_notification
Location: codex-rs/tui_app_server/src/chatwidget.rs
Signature: hook_output_entry_from_notification(entry: codex_app_server_protocol::HookOutputEntry) -> codex_protocol::protocol::HookOutputEntry
Description: Converts an app-server-protocol HookOutputEntry to the core protocol HookOutputEntry, mapping the kind via a to_core() conversion and preserving the text field.

Type: Function
Name: hook_run_summary_from_notification
Location: codex-rs/tui_app_server/src/chatwidget.rs
Signature: hook_run_summary_from_notification(run: codex_app_server_protocol::HookRunSummary) -> codex_protocol::protocol::HookRunSummary
Description: Converts an app-server-protocol HookRunSummary to the core protocol HookRunSummary, mapping all fields directly using to_core() on enum types and collecting entries via hook_output_entry_from_notification.

Type: Function
Name: hook_started_event_from_notification
Location: codex-rs/tui_app_server/src/chatwidget.rs
Signature: hook_started_event_from_notification(notification: codex_app_server_protocol::HookStartedNotification) -> codex_protocol::protocol::HookStartedEvent
Description: Converts an app-server-protocol HookStartedNotification to a core HookStartedEvent for processing by the chat widget.

Type: Function
Name: hook_completed_event_from_notification
Location: codex-rs/tui_app_server/src/chatwidget.rs
Signature: hook_completed_event_from_notification(notification: codex_app_server_protocol::HookCompletedNotification) -> codex_protocol::protocol::HookCompletedEvent
Description: Converts an app-server-protocol HookCompletedNotification to a core HookCompletedEvent for processing by the chat widget.

Type: Function
Name: event_survives_session_refresh (internal to ThreadEventStore)
Location: codex-rs/tui_app_server/src/app.rs
Signature: fn event_survives_session_refresh(event: &ThreadBufferedEvent) -> bool
Description: Determines which buffered thread events persist after a session refresh. Must return true for Request events, HookStarted notifications, AND HookCompleted notifications. Previously only returned true for Request events; must now also include HookStarted and HookCompleted notification variants.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.