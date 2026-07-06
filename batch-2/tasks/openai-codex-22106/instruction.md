I've been using the TUI and noticed that when I open a side conversation from the main thread, it doesn't pick up the settings I've configured during my session.

*   The side_fork_config function must return a config where the model field equals the model currently set in the parent thread's chat widget (not the persisted app config model).

*   The side_fork_config function must return a config where model_reasoning_effort equals the reasoning effort currently set in the parent thread's chat widget (not the persisted app config value).

*   The side_fork_config function must return a config where the service_tier field equals the service tier string currently set in the parent thread's chat widget.

*   The side_fork_config function must return a config where the permissions approval_policy value equals the approval policy currently set in the parent thread's chat widget.

*   The side_fork_config function must return a config where the permissions permission_profile equals the permission profile currently set in the parent thread's chat widget.

*   The side_fork_config function must return a config where the approvals_reviewer equals the approvals reviewer currently set in the parent thread's chat widget.

*   The side_fork_config function must set developer_instructions to include the string 'You are in a side conversation, not the main thread.' without needing to preserve or append any pre-existing developer_instructions from the app config.


*   Interface details: Type: Method
Name: side_fork_config
Location: codex-rs/tui/src/app.rs (or codex-rs/tui/src/app/mod.rs)
Signature: side_fork_config(&self) -> Settings
Description: Returns an ephemeral configuration for a side conversation forked from the current thread. The returned Settings must inherit runtime values from the app's chat_widget (model, model_reasoning_effort, service_tier, permissions approval_policy, permissions permission_profile, approvals_reviewer) rather than using the persisted app.config values. The developer_instructions field must be set to contain "You are in a side conversation, not the main thread." without appending to any pre-existing developer_instructions.

Type: Method
Name: set_model
Location: codex-rs/tui/src/chat_widget.rs (or similar)
Signature: set_model(&mut self, model: &str)
Description: Sets the current model in the chat widget's runtime state.

Type: Method
Name: set_reasoning_effort
Location: codex-rs/tui/src/chat_widget.rs (or similar)
Signature: set_reasoning_effort(&mut self, effort: Option<ReasoningEffortConfig>)
Description: Sets the current reasoning effort in the chat widget's runtime state.

Type: Method
Name: set_service_tier
Location: codex-rs/tui/src/chat_widget.rs (or similar)
Signature: set_service_tier(&mut self, tier: Option<String>)
Description: Sets the current service tier in the chat widget's runtime state.

Type: Method
Name: set_approval_policy
Location: codex-rs/tui/src/chat_widget.rs (or similar)
Signature: set_approval_policy(&mut self, policy: AskForApproval)
Description: Sets the current approval policy in the chat widget's runtime state.

Type: Method
Name: set_permission_profile
Location: codex-rs/tui/src/chat_widget.rs (or similar)
Signature: set_permission_profile(&mut self, profile: PermissionProfile) -> Result<(), _>
Description: Sets the current permission profile in the chat widget's runtime state.

Type: Method
Name: set_approvals_reviewer
Location: codex-rs/tui/src/chat_widget.rs (or similar)
Signature: set_approvals_reviewer(&mut self, reviewer: ApprovalsReviewer)
Description: Sets the current approvals reviewer in the chat widget's runtime state.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.