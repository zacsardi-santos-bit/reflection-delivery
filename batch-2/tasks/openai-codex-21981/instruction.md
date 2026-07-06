I'm working on a project that supports both interactive sessions (where a user types the first message) and goal-driven sessions (where an automated objective is set before any user interaction).

*   The `ThreadItem` struct must include a new `preview: Option<String>` field alongside the existing `first_user_message` field. All code that constructs or pattern-matches `ThreadItem` must include this field.

*   The `ThreadMetadata` struct must include a new `preview: Option<String>` field. The builder must initialize it to `None`, and field comparison logic must treat differences in `preview` as a metadata change.

*   When a session file begins with a goal-update event (containing a non-empty objective) and has no subsequent user message, `get_threads()` must return a `ThreadItem` where `preview` equals the goal objective text and `first_user_message` is `None`.

*   When a session file begins with a goal-update event followed by a later user message, `get_threads()` must return a `ThreadItem` where `preview` equals the goal objective text and `first_user_message` equals the user's message text. The goal objective takes priority as the preview.

*   For normal sessions (no goal event, first meaningful event is a user message), `get_threads()` must return a `ThreadItem` where both `preview` and `first_user_message` equal the user's message text.

*   A thread must be included in listings only when its `preview` field is populated (non-empty/non-None). Threads with no preview — whether from a user message or a goal — must be filtered out.

*   When upserting thread metadata to the state database, the `preview` field on `ThreadMetadata` must be persisted. A non-empty incoming preview must not overwrite an already-stored non-empty preview (the existing value is preserved on conflict).


*   Interface details: Type: Struct Field
Name: preview
Location: codex-rs/rollout/src/list.rs
Signature: preview: Option<String>
Description: New optional field added to the `ThreadItem` struct. Holds the best available user-facing preview text for a thread in discovery/list views. For normal sessions this equals the first user message; for goal-initiated sessions this equals the goal objective. Must appear alongside the existing `first_user_message: Option<String>` field.

Type: Struct Field
Name: preview
Location: codex-rs/state/src/model/thread_metadata.rs
Description: New optional field added to the `ThreadMetadata` struct. Holds the best available user-facing preview string for a thread. Initialized to `None` by the builder. Must participate in diff detection (field comparison in `diff_fields` or equivalent). Used when upserting threads to the state database.
Signature: preview: Option<String>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.