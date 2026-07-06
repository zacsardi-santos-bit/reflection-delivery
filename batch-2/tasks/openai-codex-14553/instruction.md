I need to add a new boolean parameter to the functions that generate permissions instructions for AI agents in our system.

*   The functions in codex-rs/protocol/src/models.rs that generate permissions instructions for DeveloperInstructions must each accept a new boolean parameter, request_permissions_tool_enabled, appended after the existing exec_permission_approvals_enabled parameter.

*   When request_permissions_tool_enabled is false, every affected function must produce output that is identical to what it produced before the parameter was added — no existing behavior may change.

*   All call sites in codex-rs/core/src/codex.rs and codex-rs/core/src/context_manager/updates.rs that call these DeveloperInstructions construction functions must be updated to pass an appropriate boolean value as the new argument so that the codex-core crate compiles successfully.

*   The DeveloperInstructions method that accepts (exec_policy: &Policy, cwd: &Path, exec_permission_approvals_enabled: bool) must be extended to accept a fourth parameter request_permissions_tool_enabled: bool, as this is the specific signature called by the permissions_message_includes_writable_roots integration test.


*   Interface details: Type: Method
Name: DeveloperInstructions (variant taking exec_policy + cwd)
Location: codex-rs/protocol/src/models.rs
Signature: Accepts at minimum (exec_policy: &Policy, cwd: &Path, exec_permission_approvals_enabled: bool, request_permissions_tool_enabled: bool) and returns DeveloperInstructions
Description: The method on DeveloperInstructions called by the integration test permissions_message_includes_writable_roots. Previously took three arguments; now requires a fourth boolean request_permissions_tool_enabled. When false the output is unchanged.

Type: Method
Name: DeveloperInstructions::from
Location: codex-rs/protocol/src/models.rs
Signature: from(approval_policy: AskForApproval, exec_policy: &Policy, exec_permission_approvals_enabled: bool, request_permissions_tool_enabled: bool) -> DeveloperInstructions
Description: Core constructor that builds permissions instructions from an approval policy. Gains a new last parameter request_permissions_tool_enabled: bool.

Type: Method
Name: DeveloperInstructions::from_permissions_with_network
Location: codex-rs/protocol/src/models.rs
Signature: from_permissions_with_network(sandbox_mode: SandboxMode, network_access: NetworkAccess, approval_policy: AskForApproval, exec_policy: &Policy, writable_roots: Option<Vec<WritableRoot>>, exec_permission_approvals_enabled: bool, request_permissions_tool_enabled: bool) -> DeveloperInstructions
Description: Builds full permissions instructions including network access. Gains a new last parameter request_permissions_tool_enabled: bool.

Note: All call sites for DeveloperInstructions construction in codex-rs/core/src/codex.rs and codex-rs/core/src/context_manager/updates.rs must be updated to pass the value of the RequestPermissionsTool feature flag as the new boolean argument.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.