I'm working on the security review component in our Rust codebase that renders a transcript of recent conversation history for a human or automated reviewer.

*   When format_guardian_action_pretty is called on a guardian action containing a very large string field (such as a patch with many thousands of lines), the returned string must contain a truncation marker in the form `<truncated omitted_approx_tokens=N>` (where N is the approximate token count of the omitted content), must be shorter in total length than the original untruncated content, and must still include the tool identifier (e.g. `"tool": "apply_patch"`).

*   render_guardian_transcript_entries must accept a slice of GuardianTranscriptEntry values and return a tuple of (Vec<String>, Option<String>).

*   When render_guardian_transcript_entries is called with entries whose combined user-message content exceeds the token budget, recent tool entries (the last tool call and the last tool result) must still appear in the returned Vec<String>, even if older user messages must be dropped.

*   In the Vec<String> returned by render_guardian_transcript_entries, tool call entries must be formatted so that the entry contains the tool label followed by a colon (e.g. "tool shell call:") and includes the key details from the tool's JSON payload (such as command arguments and URLs).

*   In the Vec<String> returned by render_guardian_transcript_entries, tool result entries must be formatted so that the entry contains the tool label followed by a colon and the result text (e.g. "tool shell result: sandbox blocked outbound network access").

*   In the Vec<String> returned by render_guardian_transcript_entries, user entries must be formatted with a numeric prefix in the form "[N] user: " where N is the entry's positional index.

*   When render_guardian_transcript_entries omits any entries due to token budget constraints, the returned Option<String> must be Some("Some conversation entries were omitted."). When no entries are omitted, it must be None.

*   GuardianTranscriptEntry must be a struct with two fields: kind (of type GuardianTranscriptEntryKind) and text (of type String).

*   GuardianTranscriptEntryKind must be an enum with at least two variants: User (no payload) and Tool(String) where the String carries the tool label.


*   Interface details: Type: Function
Name: format_guardian_action_pretty
Location: codex-rs/core/src/guardian/mod.rs (or equivalent guardian module file)
Signature: format_guardian_action_pretty(action: &GuardianApprovalRequest) -> serde_json::Result<String>
Description: Formats a guardian approval request into a pretty-printed string for display. When large string fields (such as patch text) are truncated, the truncation must be annotated with a marker of the form `<truncated omitted_approx_tokens=N>` where N is the approximate number of tokens omitted. The resulting string must be shorter than the original un-truncated content and must still include the tool identifier field.

Type: Function
Name: render_guardian_transcript_entries
Location: codex-rs/core/src/guardian/mod.rs (or equivalent guardian module file)
Signature: render_guardian_transcript_entries(entries: &[GuardianTranscriptEntry]) -> (Vec<String>, Option<String>)
Description: Renders a slice of guardian transcript entries into a list of formatted strings and an optional omission message. Must preserve recent tool context (tool call and tool result entries) even when user history alone would fill the token budget. Returns a Vec<String> of formatted entries and an Option<String> that is Some("Some conversation entries were omitted.") when any entries were dropped.

Type: Struct
Name: GuardianTranscriptEntry
Location: codex-rs/core/src/guardian/mod.rs (or equivalent guardian module file)
Description: Represents a single entry in a guardian review transcript.
Fields:
  kind: GuardianTranscriptEntryKind
  text: String

Type: Enum
Name: GuardianTranscriptEntryKind
Location: codex-rs/core/src/guardian/mod.rs (or equivalent guardian module file)
Description: Discriminates the kind of a guardian transcript entry.
Variants:
  User                 — a user-authored message
  Tool(String)         — a tool invocation or result, where the String is the tool label (e.g. "tool shell call", "tool shell result")


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.