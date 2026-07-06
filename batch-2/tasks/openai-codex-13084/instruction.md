I'm working with the app server's thread API and I've noticed that thread objects don't tell you whether they're ephemeral or not.

*   The Thread struct must include a boolean field named `ephemeral` that indicates whether the thread is temporary (true) or persistent/saved (false).

*   When a new persistent thread is created via thread/start, the Thread object in the response must have `ephemeral` set to false, and the serialized JSON must include the key `"ephemeral"` with value `false`.

*   When an ephemeral thread is created via thread/start, the Thread object in the response must have `ephemeral` set to true, and the serialized JSON must include the key `"ephemeral"` with value `true`.

*   The thread/started notification emitted when a new persistent thread is created must include the `"ephemeral": false` field in the serialized thread JSON.

*   The thread/read operation must serialize the `ephemeral` field as `"ephemeral": false` for stored rollout threads.

*   The thread/list operation must serialize the `ephemeral` field as `"ephemeral": false` for stored rollout threads.

*   The thread/resume operation must serialize the `ephemeral` field as `"ephemeral": false` for stored rollout threads.


*   Interface details: Type: Struct Field
Name: ephemeral
Location: codex-rs/app-server-protocol/src/protocol/v2.rs (field on the Thread struct)
Signature: pub ephemeral: bool
Description: A boolean field on the Thread struct indicating whether the thread is ephemeral (true) or persistent/stored (false). Must be directly accessible on the typed Thread value and must serialize to JSON as the key "ephemeral" with a boolean value. For threads backed by a saved rollout on disk, this must be false. For threads started as ephemeral (not materialized to disk), this must be true.

Type: Struct
Name: Thread
Location: codex-rs/app-server-protocol/src/protocol/v2.rs
Description: The Thread struct returned by thread/start, thread/read, thread/list, thread/resume, and the thread/started notification. Must include the new `ephemeral: bool` field. Persistent threads (stored rollouts) have ephemeral = false; ephemeral threads have ephemeral = true. The field must appear in the serialized JSON wire format under the exact key "ephemeral" with a boolean value.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.