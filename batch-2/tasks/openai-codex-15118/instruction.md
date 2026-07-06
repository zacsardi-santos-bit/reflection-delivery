I'm working with the hook system in this codebase and I've noticed that when the stop hook or the user-prompt-submit hook fires, the JSON data passed to the hook script includes session-level information but nothing that identifies the current conversation turn.

*   The stop hook input payload (passed as JSON via stdin to hook scripts) must include a 'turn_id' field containing a non-empty string that identifies the current conversation turn.

*   The user-prompt-submit hook input payload (passed as JSON via stdin to hook scripts) must include a 'turn_id' field containing a non-empty string that identifies the current conversation turn.

*   When the stop hook fires multiple times within the same conversation turn, all invocations must receive the same 'turn_id' value.

*   When the user-prompt-submit hook fires for multiple prompts within the same conversation turn (including both blocked and accepted prompts), all invocations must receive the same 'turn_id' value.

*   The user-prompt-submit hook must be invoked for every submitted prompt — both those that are blocked and those that are accepted — with the 'prompt' field set to the text of the submitted prompt.

*   The 'turn_id' field must be included in the JSON schema for both the stop hook input and the user-prompt-submit hook input as a required string property. In the Rust implementation, 'StopCommandInput' and 'UserPromptSubmitCommandInput' structs in 'codex-rs/hooks/src/schema.rs' must each include a 'pub turn_id: String' field that is serialized into the JSON payload.


*   Interface details: Type: Struct
Name: StopCommandInput
Location: codex-rs/hooks/src/schema.rs
Description: Represents the JSON input payload serialized and passed via stdin to stop hook scripts. Must include a new required field 'turn_id' of type String. The field identifies the active conversation turn and must be populated from the hook request.
Signature: pub turn_id: String  (new required field, placed after session_id and before transcript_path in the struct definition)

Type: Struct
Name: UserPromptSubmitCommandInput
Location: codex-rs/hooks/src/schema.rs
Description: Represents the JSON input payload serialized and passed via stdin to user-prompt-submit hook scripts. Must include a new required field 'turn_id' of type String. The field identifies the active conversation turn and must be populated from the hook request.
Signature: pub turn_id: String  (new required field, placed after session_id and before transcript_path in the struct definition)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.