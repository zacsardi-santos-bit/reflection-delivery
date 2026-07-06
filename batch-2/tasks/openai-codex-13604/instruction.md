I'm working on the feedback consent flow in a terminal UI application.

*   The feedback_upload_consent_params function must replace its boolean fourth parameter with a reference to a FeedbackDiagnostics value. The old parameter indicated only whether connectivity diagnostics existed; the new parameter carries the full diagnostic data.

*   When the FeedbackDiagnostics passed to feedback_upload_consent_params is non-empty, the consent popup header must include the connectivity diagnostics attachment filename (codex-connectivity-diagnostics.txt) in the list of files to be shared.

*   When the feedback category is Bug or GoodResult and the FeedbackDiagnostics is non-empty, the consent popup header must render a 'Connectivity diagnostics' section after the file list. This section must show each diagnostic's headline prefixed with '  - ' and each detail string prefixed with '    - '.

*   FeedbackDiagnostics must expose a public constructor new(diagnostics: Vec<FeedbackDiagnostic>) -> FeedbackDiagnostics that wraps the provided diagnostics.

*   FeedbackDiagnostics must expose an is_empty(&self) -> bool method that returns true when the diagnostics collection contains no entries.

*   FeedbackDiagnostics must expose a diagnostics method that returns the contained FeedbackDiagnostic entries (as a slice or equivalent), where each FeedbackDiagnostic has public fields headline: String and details: Vec<String>.

*   The call site in the chat widget that previously extracted a boolean from the snapshot's diagnostic attachment text must instead pass the full FeedbackDiagnostics reference obtained from the snapshot to feedback_upload_consent_params.

*   The feedback note entry view must no longer render connectivity diagnostics inline; the diagnostics display must only appear in the consent popup.


*   Interface details: Type: Struct
Name: FeedbackDiagnostic
Location: codex-rs/feedback/src/feedback_diagnostics.rs
Description: Represents a single connectivity diagnostic entry with a headline message and a list of detail strings. Both fields must be public.
Signature: struct FeedbackDiagnostic { headline: String, details: Vec<String> }

Type: Struct/Impl
Name: FeedbackDiagnostics
Location: codex-rs/feedback/src/feedback_diagnostics.rs
Description: A collection of FeedbackDiagnostic entries. Must provide a public `new` constructor accepting a Vec<FeedbackDiagnostic>, an `is_empty` method returning bool, and a `diagnostics` method returning a slice or iterator over the contained FeedbackDiagnostic entries.
Signature:
  new(diagnostics: Vec<FeedbackDiagnostic>) -> FeedbackDiagnostics
  is_empty(&self) -> bool
  diagnostics(&self) -> &[FeedbackDiagnostic]  (or equivalent iterator)

Type: Function
Name: feedback_upload_consent_params
Location: codex-rs/tui/src/bottom_pane/feedback_view.rs
Description: Builds the SelectionViewParams for the feedback upload consent popup. The fourth parameter must change from a boolean to a reference to FeedbackDiagnostics. When the diagnostics are non-empty, the attachment filename for connectivity diagnostics must appear in the header. When the category is one that shows connectivity details (Bug or GoodResult), a "Connectivity diagnostics" section must be rendered inline in the popup header, listing each diagnostic headline prefixed with "  - " and each detail line prefixed with "    - ".
Signature: feedback_upload_consent_params(app_event_tx: AppEventSender, category: FeedbackCategory, rollout_path: Option<std::path::PathBuf>, feedback_diagnostics: &FeedbackDiagnostics) -> SelectionViewParams


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.