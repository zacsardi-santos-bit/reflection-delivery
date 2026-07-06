I'm working on improving observability for MCP tool calls in the Rust codex-rs implementation.

*   The function truncate_str_to_char_boundary must accept a string slice and a maximum character count, returning a string slice truncated to at most that many UTF-8 characters. If the string is shorter than the limit, it must be returned unchanged.

*   The truncation performed by truncate_str_to_char_boundary must always occur at valid UTF-8 character boundaries — it must never split a multi-byte character. For example, truncating a string of multi-byte characters (such as two-byte Unicode characters) at a limit of 256 must return exactly 256 complete characters.

*   The constant MCP_RESULT_TELEMETRY_TARGET_ID_MAX_CHARS must be defined as usize = 256, and used as the character limit when truncating target_id values recorded to spans.

*   The function record_mcp_result_span_telemetry must read telemetry from an optional CallToolResult. When the result's meta contains a JSON object at key 'codex/telemetry', and that object contains a 'span' object, the function must extract and record allowlisted fields to the tracing span.

*   When the span telemetry object contains a 'target_id' field whose value is a non-empty string, record_mcp_result_span_telemetry must record it to the span attribute named 'codex.mcp.target.id', truncated to MCP_RESULT_TELEMETRY_TARGET_ID_MAX_CHARS characters using truncate_str_to_char_boundary.

*   When the span telemetry object contains a 'did_trigger_server_user_flow' field whose value is a boolean, record_mcp_result_span_telemetry must record it to the span attribute named 'codex.mcp.server_user_flow.triggered'.

*   The function record_mcp_result_span_telemetry must ignore any keys in the span telemetry object that are not 'target_id' or 'did_trigger_server_user_flow' — no unknown keys should be promoted to span attributes.

*   The function record_mcp_result_span_telemetry must ignore 'target_id' if its value is not a string (e.g., an integer), and must ignore 'did_trigger_server_user_flow' if its value is not a boolean (e.g., a string). Neither attribute should appear in the span in those cases.

*   The function record_mcp_result_span_telemetry must do nothing when: the result is None, the result has no meta, the meta is not a JSON object, the 'codex/telemetry' key is absent, or the 'span' key within the telemetry object is absent or not an object.

*   The tracing span created for MCP tool calls must pre-register 'codex.mcp.target.id' and 'codex.mcp.server_user_flow.triggered' as Empty fields so that record_mcp_result_span_telemetry can populate them after the tool call completes.


*   Interface details: Type: Function
Name: truncate_str_to_char_boundary
Location: codex-rs/core/src/mcp_tool_call.rs
Signature: truncate_str_to_char_boundary(value: &str, max_chars: usize) -> &str
Description: Truncates a string to at most max_chars UTF-8 characters (not bytes). If the string is shorter than max_chars, returns it unchanged. Must not split multi-byte UTF-8 characters — only truncates at valid character boundaries.

Type: Function
Name: record_mcp_result_span_telemetry
Location: codex-rs/core/src/mcp_tool_call.rs
Signature: record_mcp_result_span_telemetry(span: &Span, result: Option<&CallToolResult>)
Description: Extracts telemetry from an MCP CallToolResult's metadata and records it as span attributes. Navigates the path result.meta["codex/telemetry"]["span"]. If the "target_id" field is a non-empty string, records it (truncated to MCP_RESULT_TELEMETRY_TARGET_ID_MAX_CHARS characters) to the span attribute "codex.mcp.target.id". If "did_trigger_server_user_flow" is a boolean, records it to "codex.mcp.server_user_flow.triggered". Unknown keys are ignored. If result is None, or metadata is absent or malformed, does nothing.

Type: Constant
Name: MCP_RESULT_TELEMETRY_TARGET_ID_MAX_CHARS
Location: codex-rs/core/src/mcp_tool_call.rs
Signature: const MCP_RESULT_TELEMETRY_TARGET_ID_MAX_CHARS: usize = 256
Description: Maximum number of UTF-8 characters allowed for the target_id span attribute. Used directly by name in tests.

Type: Constant
Name: MCP_RESULT_TELEMETRY_META_KEY
Location: codex-rs/core/src/mcp_tool_call.rs
Signature: const MCP_RESULT_TELEMETRY_META_KEY: &str = "codex/telemetry"
Description: JSON key in CallToolResult.meta used to locate the telemetry object.

Type: Constant
Name: MCP_RESULT_TELEMETRY_SPAN_KEY
Location: codex-rs/core/src/mcp_tool_call.rs
Signature: const MCP_RESULT_TELEMETRY_SPAN_KEY: &str = "span"
Description: JSON key within the telemetry object to locate span telemetry fields.

Type: Constant
Name: MCP_RESULT_TELEMETRY_TARGET_ID_KEY
Location: codex-rs/core/src/mcp_tool_call.rs
Signature: const MCP_RESULT_TELEMETRY_TARGET_ID_KEY: &str = "target_id"
Description: JSON key within the span telemetry object whose string value is recorded as the target ID span attribute.

Type: Constant
Name: MCP_RESULT_TELEMETRY_DID_TRIGGER_SERVER_USER_FLOW_KEY
Location: codex-rs/core/src/mcp_tool_call.rs
Signature: const MCP_RESULT_TELEMETRY_DID_TRIGGER_SERVER_USER_FLOW_KEY: &str = "did_trigger_server_user_flow"
Description: JSON key within the span telemetry object whose boolean value is recorded as the server user flow span attribute.

Type: Constant
Name: MCP_RESULT_TELEMETRY_TARGET_ID_SPAN_ATTR
Location: codex-rs/core/src/mcp_tool_call.rs
Signature: const MCP_RESULT_TELEMETRY_TARGET_ID_SPAN_ATTR: &str = "codex.mcp.target.id"
Description: Span attribute name used when recording the target ID from MCP result telemetry.

Type: Constant
Name: MCP_RESULT_TELEMETRY_SERVER_USER_FLOW_SPAN_ATTR
Location: codex-rs/core/src/mcp_tool_call.rs
Signature: const MCP_RESULT_TELEMETRY_SERVER_USER_FLOW_SPAN_ATTR: &str = "codex.mcp.server_user_flow.triggered"
Description: Span attribute name used when recording the server user flow flag from MCP result telemetry.

Note: The span created by mcp_tool_call_span must pre-register "codex.mcp.target.id" and "codex.mcp.server_user_flow.triggered" as Empty fields so that record_mcp_result_span_telemetry can set them later.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.