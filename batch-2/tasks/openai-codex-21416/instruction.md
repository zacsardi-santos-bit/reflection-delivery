I'm working on a Rust project that has tool specification factories — functions that describe each tool (its name, JSON schema, description, and parameters) to the language model — living in a separate utilities crate.

*   The file codex-rs/core/src/tools/handlers/shell_spec.rs must exist and export create_shell_command_tool(), create_exec_command_tool(options: CommandToolOptions), create_shell_tool(options: ShellToolOptions), and create_write_stdin_tool(), each returning the correct ToolSpec structure (identical to the existing implementations in codex-rs/tools/src/local_tool.rs, updating imports from crate:: to codex_tools::).

*   The test file for shell_spec must be at codex-rs/core/src/tools/handlers/shell_spec_tests.rs and referenced from shell_spec.rs via `#[cfg(test)] #[path = "shell_spec_tests.rs"] mod tests;`.

*   The file codex-rs/core/src/tools/handlers/mcp_resource_spec.rs must exist and export create_list_mcp_resources_tool(), create_read_mcp_resource_tool(), and create_list_mcp_resource_templates_tool(), each returning the correct ToolSpec (moved from codex-rs/tools/src/mcp_resource_tool.rs, updating imports).

*   The test file for mcp_resource_spec must be at codex-rs/core/src/tools/handlers/mcp_resource_spec_tests.rs referenced via `#[path = "mcp_resource_spec_tests.rs"]`.

*   The file codex-rs/core/src/tools/handlers/apply_patch_spec.rs must exist and export create_apply_patch_freeform_tool() and create_apply_patch_json_tool() returning the correct ToolSpec, plus the ApplyPatchToolArgs struct. It must include the Lark grammar via include_str!("apply_patch.lark"). The grammar file must be moved to codex-rs/core/src/tools/handlers/apply_patch.lark.

*   The test file for apply_patch_spec must be at codex-rs/core/src/tools/handlers/apply_patch_spec_tests.rs referenced via `#[path = "apply_patch_spec_tests.rs"]`.

*   The file codex-rs/core/src/tools/handlers/test_sync_spec.rs must exist and export create_test_sync_tool() returning the correct ToolSpec (moved from codex-rs/tools/src/utility_tool.rs, updating imports). The test file must be at codex-rs/core/src/tools/handlers/test_sync_spec_tests.rs referenced via `#[path = "test_sync_spec_tests.rs"]`.

*   The file codex-rs/core/src/tools/hosted_spec.rs must exist and export create_image_generation_tool(output_format: &str) which returns ToolSpec::ImageGeneration { output_format: output_format.to_string() }. When called with "png" it returns ToolSpec::ImageGeneration { output_format: "png".to_string() }.

*   The hosted_spec.rs must also export create_web_search_tool(options: WebSearchToolOptions<'_>) -> Option<ToolSpec> and the WebSearchToolOptions struct. The test file must be at codex-rs/core/src/tools/hosted_spec_tests.rs referenced via `#[cfg(test)] #[path = "hosted_spec_tests.rs"] mod tests;`.

*   The file codex-rs/core/src/tools/code_mode/execute_spec.rs must exist and export pub(crate) fn create_code_mode_tool(enabled_tools: &[codex_code_mode::ToolDefinition], namespace_descriptions: &BTreeMap<String, codex_code_mode::ToolNamespaceDescription>, code_mode_only: bool, deferred_tools_available: bool) -> ToolSpec. It must return ToolSpec::Freeform with the name codex_code_mode::PUBLIC_TOOL_NAME, the description from codex_code_mode::build_exec_tool_description, and a FreeformToolFormat with type="grammar", syntax="lark", and the exact grammar string (starting with a newline then 'start: pragma_source | plain_source', ending with 'SOURCE: /[\s\S]+/' followed by a newline).

*   The file codex-rs/core/src/tools/code_mode/wait_spec.rs must exist and export pub(crate) fn create_wait_tool() -> ToolSpec. It must return ToolSpec::Function(ResponsesApiTool { name: codex_code_mode::WAIT_TOOL_NAME.to_string(), strict: false, defer_loading: None, output_schema: None, description constructed as format!("Waits on a yielded `{}` cell...", ...), parameters: JsonSchema::object with properties cell_id (string), yield_time_ms (number), max_tokens (number), terminate (boolean), required=["cell_id"], additional_properties=false }).

*   The module declarations in codex-rs/core/src/tools/handlers/mod.rs must include pub(crate) mod apply_patch_spec, pub(crate) mod mcp_resource_spec, pub(crate) mod shell_spec, and pub(crate) mod test_sync_spec.

*   The module declaration in codex-rs/core/src/tools/mod.rs must include pub(crate) mod hosted_spec.

*   The module declarations in codex-rs/core/src/tools/code_mode/mod.rs must include pub(crate) mod execute_spec and pub(crate) mod wait_spec.

*   codex-rs/core/src/goals.rs must import UPDATE_GOAL_TOOL_NAME from crate::tools::handlers::goal_spec instead of from codex_tools. codex-rs/core/src/tools/handlers/apply_patch.rs must import ApplyPatchToolArgs from crate::tools::handlers::apply_patch_spec instead of from codex_tools.


*   Interface details: Type: File
Name: shell_spec.rs
Location: codex-rs/core/src/tools/handlers/shell_spec.rs
Description: Tool specification factories for shell-related tools. Contains public functions for creating shell tool specs. Renamed/moved from codex-rs/tools/src/local_tool.rs. The test file must be located at codex-rs/core/src/tools/handlers/shell_spec_tests.rs (referenced via `#[path = "shell_spec_tests.rs"]`).
Signature:
  pub fn create_shell_command_tool() -> ToolSpec
  pub fn create_exec_command_tool(options: CommandToolOptions) -> ToolSpec
  pub fn create_shell_tool(options: ShellToolOptions) -> ToolSpec
  pub fn create_write_stdin_tool() -> ToolSpec

---

Type: File
Name: mcp_resource_spec.rs
Location: codex-rs/core/src/tools/handlers/mcp_resource_spec.rs
Description: Tool specification factories for MCP resource tools. Renamed/moved from codex-rs/tools/src/mcp_resource_tool.rs. Uses `codex_tools::JsonSchema`, `codex_tools::ResponsesApiTool`, `codex_tools::ToolSpec`. The test file must be at codex-rs/core/src/tools/handlers/mcp_resource_spec_tests.rs (referenced via `#[path = "mcp_resource_spec_tests.rs"]`).
Signature:
  pub fn create_list_mcp_resources_tool() -> ToolSpec
  pub fn create_read_mcp_resource_tool() -> ToolSpec
  pub fn create_list_mcp_resource_templates_tool() -> ToolSpec

---

Type: File
Name: apply_patch_spec.rs
Location: codex-rs/core/src/tools/handlers/apply_patch_spec.rs
Description: Tool specification factories for apply-patch tools, plus the ApplyPatchToolArgs type. Renamed/moved from codex-rs/tools/src/apply_patch_tool.rs. Uses `codex_tools::FreeformTool`, `codex_tools::FreeformToolFormat`, `codex_tools::JsonSchema`, `codex_tools::ResponsesApiTool`, `codex_tools::ToolSpec`. The Lark grammar is loaded from `apply_patch.lark` in the same directory (was previously `tool_apply_patch.lark`). The test file must be at codex-rs/core/src/tools/handlers/apply_patch_spec_tests.rs (referenced via `#[path = "apply_patch_spec_tests.rs"]`).
Signature:
  pub fn create_apply_patch_freeform_tool() -> ToolSpec
  pub fn create_apply_patch_json_tool() -> ToolSpec
  pub struct ApplyPatchToolArgs { ... }   // (Deserialize+Serialize, same definition as in codex-rs/tools/src/apply_patch_tool.rs)

---

Type: File
Name: test_sync_spec.rs
Location: codex-rs/core/src/tools/handlers/test_sync_spec.rs
Description: Tool specification factory for the test-sync tool. Renamed/moved from codex-rs/tools/src/utility_tool.rs. Uses `codex_tools::JsonSchema`, `codex_tools::ResponsesApiTool`, `codex_tools::ToolSpec`. The test file must be at codex-rs/core/src/tools/handlers/test_sync_spec_tests.rs (referenced via `#[path = "test_sync_spec_tests.rs"]`).
Signature:
  pub fn create_test_sync_tool() -> ToolSpec

---

Type: File
Name: hosted_spec.rs
Location: codex-rs/core/src/tools/hosted_spec.rs
Description: Tool specification factories for hosted tools (image generation, web search). New file. Uses `codex_tools::ToolSpec`. The test file must be at codex-rs/core/src/tools/hosted_spec_tests.rs (referenced via `#[path = "hosted_spec_tests.rs"]`).
Signature:
  pub fn create_image_generation_tool(output_format: &str) -> ToolSpec
  pub fn create_web_search_tool(options: WebSearchToolOptions<'_>) -> Option<ToolSpec>
  pub struct WebSearchToolOptions<'a> {
      pub web_search_mode: Option<WebSearchMode>,
      pub web_search_config: Option<&'a WebSearchConfig>,
      pub web_search_tool_type: WebSearchToolType,
  }

---

Type: Function
Name: create_image_generation_tool
Location: codex-rs/core/src/tools/hosted_spec.rs
Signature: pub fn create_image_generation_tool(output_format: &str) -> ToolSpec
Description: Returns ToolSpec::ImageGeneration { output_format: output_format.to_string() }. When called with "png", returns ToolSpec::ImageGeneration { output_format: "png".to_string() }.

---

Type: File
Name: execute_spec.rs
Location: codex-rs/core/src/tools/code_mode/execute_spec.rs
Description: Tool specification factory for code execution mode. New file. Uses `codex_code_mode::ToolDefinition as CodeModeToolDefinition`, `codex_tools::FreeformTool`, `codex_tools::FreeformToolFormat`, `codex_tools::ToolSpec`. Must be added to the code_mode module (`pub(crate) mod execute_spec` in codex-rs/core/src/tools/code_mode/mod.rs).
Signature:
  pub(crate) fn create_code_mode_tool(
      enabled_tools: &[codex_code_mode::ToolDefinition],
      namespace_descriptions: &BTreeMap<String, codex_code_mode::ToolNamespaceDescription>,
      code_mode_only: bool,
      deferred_tools_available: bool,
  ) -> ToolSpec

The function returns ToolSpec::Freeform(FreeformTool {
    name: codex_code_mode::PUBLIC_TOOL_NAME.to_string(),
    description: codex_code_mode::build_exec_tool_description(enabled_tools, namespace_descriptions, code_mode_only, deferred_tools_available),
    format: FreeformToolFormat {
        r#type: "grammar".to_string(),
        syntax: "lark".to_string(),
        definition: <LARK_GRAMMAR>.to_string(),
    },
})

The Lark grammar definition (stored as a const named CODE_MODE_FREEFORM_GRAMMAR) is:
```
start: pragma_source | plain_source
pragma_source: PRAGMA_LINE NEWLINE SOURCE
plain_source: SOURCE

PRAGMA_LINE: /[ \t]*\/\/ @exec:[^\r\n]*/
NEWLINE: /\r?\n/
SOURCE: /[\s\S]+/
```
(Note: there is a leading newline before `start:` and a trailing newline after `SOURCE: /[\s\S]+/`)

---

Type: File
Name: wait_spec.rs
Location: codex-rs/core/src/tools/code_mode/wait_spec.rs
Description: Tool specification factory for the wait operation in code execution mode. New file. Uses `codex_tools::JsonSchema`, `codex_tools::ResponsesApiTool`, `codex_tools::ToolSpec`. Must be added to the code_mode module (`pub(crate) mod wait_spec` in codex-rs/core/src/tools/code_mode/mod.rs).
Signature:
  pub(crate) fn create_wait_tool() -> ToolSpec

The function returns ToolSpec::Function(ResponsesApiTool {
    name: codex_code_mode::WAIT_TOOL_NAME.to_string(),
    description: format!("Waits on a yielded `{}` cell and returns new output or completion.\n{}", codex_code_mode::PUBLIC_TOOL_NAME, codex_code_mode::build_wait_tool_description().trim()),
    strict: false,
    parameters: JsonSchema::object(
        BTreeMap containing:
          "cell_id" -> JsonSchema::string(Some("Identifier of the running exec cell.".to_string())),
          "yield_time_ms" -> JsonSchema::number(Some("How long to wait (in milliseconds) for more output before yielding again.".to_string())),
          "max_tokens" -> JsonSchema::number(Some("Maximum number of output tokens to return for this wait call.".to_string())),
          "terminate" -> JsonSchema::boolean(Some("Whether to terminate the running exec cell.".to_string())),
        required: Some(vec!["cell_id".to_string()]),
        additional_properties: Some(false.into()),
    ),
    output_schema: None,
    defer_loading: None,
})

---

Type: File
Name: apply_patch.lark
Location: codex-rs/core/src/tools/handlers/apply_patch.lark
Description: The Lark grammar file for apply-patch, renamed/moved from codex-rs/tools/src/tool_apply_patch.lark. The apply_patch_spec.rs references this file via include_str!("apply_patch.lark").

---

Type: Module updates
Name: handlers/mod.rs
Location: codex-rs/core/src/tools/handlers/mod.rs
Description: Must export the new spec modules. Add the following public(crate) module declarations:
  pub(crate) mod apply_patch_spec;
  pub(crate) mod mcp_resource_spec;
  pub(crate) mod shell_spec;
  pub(crate) mod test_sync_spec;
  (Plus other spec modules being added: agent_jobs_spec, goal_spec, multi_agents_spec, plan_spec, request_plugin_install_spec, request_user_input_spec, tool_search_spec, view_image_spec)

Type: Module updates
Name: tools/mod.rs
Location: codex-rs/core/src/tools/mod.rs
Description: Must export the new hosted_spec module. Add: pub(crate) mod hosted_spec;

Type: Module updates
Name: code_mode/mod.rs
Location: codex-rs/core/src/tools/code_mode/mod.rs
Description: Must export the new execute_spec and wait_spec modules. Add:
  pub(crate) mod execute_spec;
  pub(crate) mod wait_spec;

---

Type: Import update
Name: goals.rs
Location: codex-rs/core/src/goals.rs
Description: Must import UPDATE_GOAL_TOOL_NAME from the new location. Change from `codex_tools::UPDATE_GOAL_TOOL_NAME` to `crate::tools::handlers::goal_spec::UPDATE_GOAL_TOOL_NAME`.

Type: Import update
Name: apply_patch.rs
Location: codex-rs/core/src/tools/handlers/apply_patch.rs
Description: Must import ApplyPatchToolArgs from the new location. Change from `codex_tools::ApplyPatchToolArgs` to `crate::tools::handlers::apply_patch_spec::ApplyPatchToolArgs`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.