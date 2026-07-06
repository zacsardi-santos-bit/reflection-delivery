I'm working on the TUI chat application and running into an issue with repeated warning messages cluttering the conversation history.

*   Must create a new module at codex-rs/tui/src/chatwidget/warnings.rs containing a WarningDisplayState struct that implements Default (via #[derive(Default)] or manually).

*   WarningDisplayState must expose a method with signature should_display(&mut self, message: &str) -> bool that decides whether a warning message should be added to the chat history.

*   When should_display is called with a warning that matches the exact pattern 'Model metadata for `{slug}` not found. Defaulting to fallback metadata; this can degrade performance and cause issues.', it must return true the first time a given slug is encountered and false for every subsequent call with the same slug.

*   When should_display is called with any warning message that does not match the model metadata pattern above, it must always return true regardless of how many times it has been called with that message.

*   The ChatWidget struct in codex-rs/tui/src/chatwidget.rs must include a field named warning_display_state of type WarningDisplayState, initialized as WarningDisplayState::default() in the ChatWidget constructor.

*   The warning handling logic (the method that adds warnings to conversation history) must call warning_display_state.should_display(&message) before adding a warning; if should_display returns false, the warning must be silently dropped and not added to history.

*   When a model metadata warning is added to history, the rendered cell content must contain the model slug extracted from the warning (e.g., the cell for a warning about 'unknown-model' must contain the text 'unknown-model').


*   Interface details: Type: Struct
Name: WarningDisplayState
Location: codex-rs/tui/src/chatwidget/warnings.rs
Description: Tracks which warnings have already been displayed, enabling deduplication of model-specific metadata warnings. Must derive or implement Default. Must be pub(super) or accessible from within the chatwidget module.
Signature: should_display(&mut self, message: &str) -> bool
  - Returns true if the warning should be displayed (added to history), false if it should be suppressed.
  - For warnings matching the model metadata pattern "Model metadata for `{slug}` not found. Defaulting to fallback metadata; this can degrade performance and cause issues.", returns true the first time a given slug is seen, and false for all subsequent occurrences of the same slug.
  - For all other (generic) warning messages, always returns true.

Type: Field addition
Name: warning_display_state
Location: codex-rs/tui/src/chatwidget.rs (in the ChatWidget struct)
Description: A field of type WarningDisplayState added to the ChatWidget struct. Must be initialized as WarningDisplayState::default() in all places where ChatWidget is constructed (including tests/helpers.rs make_chatwidget_manual). The on_warning method must consult this field via should_display before adding any warning to history.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.