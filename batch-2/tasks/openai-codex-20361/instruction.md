I've been working with the realtime conversation APIs and noticed that the field used to carry the session identifier is named in a way that's too generic — just "session ID.

*   The ConversationStartParams struct must have a field named realtime_session_id of type Option<String>. The field previously named session_id must be renamed to realtime_session_id. Both None and Some(String) values must be accepted.

*   The RealtimeEvent enum's SessionUpdated variant must expose a field named realtime_session_id of type String (instead of session_id). The variant also retains an instructions field of type Option<String>. Equality comparisons such as RealtimeEvent::SessionUpdated { realtime_session_id: "sess_mock".to_string(), instructions: Some("backend prompt".to_string()) } must succeed.

*   The RealtimeConversationStartedEvent struct must have a field named realtime_session_id of type Option<String> (instead of session_id). After a successful realtime conversation start, this field must be Some.

*   The ThreadRealtimeStartParams struct must have a field named realtime_session_id of type Option<String> (instead of session_id).

*   The ThreadRealtimeStartedNotification struct must have a field named realtime_session_id of type Option<String> (instead of session_id). After a successful realtime start, this field must be Some when a session identifier was established.


*   Interface details: Type: Struct
Name: ConversationStartParams
Location: codex-rs/protocol/src/protocol.rs
Description: Parameters for starting a realtime conversation. The field previously named session_id must be renamed to realtime_session_id.
Signature: realtime_session_id: Option<String>

Type: Enum Variant
Name: RealtimeEvent::SessionUpdated
Location: codex-rs/protocol/src/protocol.rs
Description: Variant of the RealtimeEvent enum emitted when a realtime session is updated. The field previously named session_id must be renamed to realtime_session_id. The instructions field (type Option<String>) is unchanged.
Signature: SessionUpdated { realtime_session_id: String, instructions: Option<String> }

Type: Struct
Name: RealtimeConversationStartedEvent
Location: codex-rs/protocol/src/protocol.rs
Description: Event emitted when a realtime conversation has started. The field previously named session_id must be renamed to realtime_session_id. After a successful start, realtime_session_id must be Some.
Signature: realtime_session_id: Option<String>

Type: Struct
Name: ThreadRealtimeStartParams
Location: codex-rs/app-server-protocol/src/protocol/v2.rs
Description: Protocol parameters for starting a realtime thread via the app-server protocol. The field previously named session_id must be renamed to realtime_session_id.
Signature: realtime_session_id: Option<String>

Type: Struct
Name: ThreadRealtimeStartedNotification
Location: codex-rs/app-server-protocol/src/protocol/v2.rs
Description: Notification emitted by the app-server when thread realtime startup is accepted. The field previously named session_id must be renamed to realtime_session_id. After a successful start, realtime_session_id must be Some when a session identifier was established.
Signature: realtime_session_id: Option<String>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.