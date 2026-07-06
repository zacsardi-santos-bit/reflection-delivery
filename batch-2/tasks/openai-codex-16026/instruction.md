I'm working on the TUI for Codex and I've noticed that the terminal title and status line don't update immediately when I change the active model or switch collaboration modes.

*   ConfigBuilder must support a fallback_cwd builder method that accepts an optional path value, enabling test and embedded configurations to specify a fallback current working directory.

*   ChatWidget::set_model must automatically refresh the terminal title immediately after the model is changed. When tui_terminal_title is configured with the 'model' token, calling set_model with a new model name must update last_terminal_title to Some(new_model_name) without any additional refresh call.

*   ChatWidget::set_collaboration_mask must automatically refresh the status line immediately after the collaboration mask is changed. When tui_status_line is configured with the 'model-with-reasoning' token and the CollaborationModes feature is enabled, calling set_collaboration_mask with a plan mask must update the status line to show the model name followed by 'medium', and calling it with the default mask must restore it to show the model name followed by 'high'.

*   The collaboration_modes module must provide a plan_mask function that accepts a reference to the model catalog and returns the collaboration mask for plan mode (Option type), and a default_mask function that similarly returns the default collaboration mode mask.

*   A new UI snapshot named 'status_line_model_with_reasoning_plan_mode_footer' must exist for the status line rendered in plan collaboration mode with model 'gpt-5.3-codex' and the 'model-with-reasoning' status line token, showing 'medium' reasoning effort.

*   Snapshot tests that render terminal output containing working directory paths must normalize those paths to a canonical Unix-style representation before comparing against stored snapshots, so tests pass consistently across all operating systems including Windows.


*   Interface details: Type: Method
Name: set_model
Location: codex-rs/tui_app_server/src/chatwidget/mod.rs (or equivalent ChatWidget source file)
Signature: set_model(&mut self, model: impl Into<String>)
Description: Changes the active model for the chat widget. After calling this method, last_terminal_title must be automatically updated to reflect the new model name when tui_terminal_title is configured. No separate call to refresh_terminal_title() should be needed.

Type: Field
Name: last_terminal_title
Location: codex-rs/tui_app_server/src/chatwidget/mod.rs (or equivalent ChatWidget source file)
Signature: last_terminal_title: Option<String>
Description: Stores the most recently set terminal title string. Updated automatically by set_model when tui_terminal_title is configured.

Type: Method
Name: set_collaboration_mask
Location: codex-rs/tui_app_server/src/chatwidget/mod.rs (or equivalent ChatWidget source file)
Signature: set_collaboration_mask(&mut self, mask: CollaborationMask)
Description: Changes the active collaboration mode mask. After calling this method, the status line text returned by status_line_text must be automatically updated to reflect the new mode's reasoning effort level. No separate refresh call should be needed.

Type: Function
Name: plan_mask
Location: codex-rs/tui_app_server/src/chatwidget/collaboration_modes.rs (or equivalent)
Signature: plan_mask(catalog: &ModelCatalog) -> Option<CollaborationMask>
Description: Returns the collaboration mask representing plan mode. Returns None if plan mode is not available in the catalog. When this mask is active with the "model-with-reasoning" status line token, the status line displays "medium" as the reasoning effort.

Type: Function
Name: default_mask
Location: codex-rs/tui_app_server/src/chatwidget/collaboration_modes.rs (or equivalent)
Signature: default_mask(catalog: &ModelCatalog) -> Option<CollaborationMask>
Description: Returns the default collaboration mode mask. Returns None if the default mode is not available in the catalog. When this mask is active with the "model-with-reasoning" status line token, the status line displays "high" as the reasoning effort.

Type: Method
Name: fallback_cwd
Location: codex-rs/core/src/config.rs (or equivalent ConfigBuilder source file)
Signature: fallback_cwd(self, cwd: Option<PathBuf>) -> ConfigBuilder
Description: Builder method on ConfigBuilder that sets the fallback current working directory. Used when no explicit working directory has been established, such as in embedded or test configurations.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.