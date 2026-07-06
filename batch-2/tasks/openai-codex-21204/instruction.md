I'm working on a memory search backend and want to add a new proximity-based matching mode.

*   The SearchMatchMode enum must rename the existing All variant to AllOnSameLine, and add a new AllWithinLines variant with a line_count: usize field. The enum must use tagged union serialization with tag field 'type' and snake_case variant names; AllWithinLines serializes as {"type": "all_within_lines", "line_count": N}.

*   The MemoriesBackendError enum must include a unit variant InvalidMatchWindow (no fields) with error message 'all_within_lines.line_count must be a positive integer'.

*   When search() is called with SearchMatchMode::AllWithinLines and line_count equal to 0, the method must return Err(MemoriesBackendError::InvalidMatchWindow) without performing any search.

*   When search() is called with SearchMatchMode::AllWithinLines and a positive line_count, the method must find all windows of consecutive lines (spanning at most line_count lines) where all queries appear at least once across those lines, reporting each minimal such window as a match.

*   When a larger AllWithinLines match window strictly contains a smaller valid window (same queries, narrower span), the larger window must be omitted from the results so only the tightest-fitting windows are returned.

*   The AllOnSameLine match mode must behave as the previous All mode: a line matches only when every query appears on that single line.

*   The InvalidMatchWindow error variant must be handled in server-level error mapping, converting it to an invalid_params MCP error using the error's string representation, consistent with how other invalid-input errors are handled.

*   When deserializing search tool call arguments that specify the AllWithinLines match mode (via type 'all_within_lines' and a line_count field), the resulting SearchMemoriesRequest must have match_mode set to SearchMatchMode::AllWithinLines { line_count } with the provided value.


*   Interface details: Type: Enum
Name: SearchMatchMode
Location: codex-rs/memories/mcp/src/backend.rs
Description: Enum representing how multiple search queries must match. The existing Any variant remains. The existing All variant must be renamed to AllOnSameLine. A new AllWithinLines variant must be added with a line_count field of type usize. The enum uses tagged union serialization with tag field "type" and snake_case variant names. Serialization of AllWithinLines uses type "all_within_lines" and includes the line_count field. The AllWithinLines variant's line_count field carries a schema annotation requiring minimum value of 1.
Variants:
  Any
  AllOnSameLine
  AllWithinLines { line_count: usize }

Type: Enum Variant
Name: InvalidMatchWindow
Location: codex-rs/memories/mcp/src/backend.rs
Description: A unit variant (no fields) of the MemoriesBackendError enum. Returned when search() receives SearchMatchMode::AllWithinLines with line_count equal to 0. The associated error message string is "all_within_lines.line_count must be a positive integer".

Type: Method
Name: search
Location: codex-rs/memories/mcp/src/local.rs
Signature: async fn search(&self, request: SearchMemoriesRequest) -> Result<SearchMemoriesResponse, MemoriesBackendError>
Description: Must validate that when request.match_mode is SearchMatchMode::AllWithinLines with line_count == 0, the method returns Err(MemoriesBackendError::InvalidMatchWindow). Must also implement AllWithinLines proximity search logic: find windows of exactly line_count consecutive lines where all queries appear at least once across those lines; when a larger window strictly contains a smaller one matching the same criteria, omit the larger window from results.

Type: Function (server error mapping)
Name: backend_error_to_mcp
Location: codex-rs/memories/mcp/src/server.rs
Description: The match arm mapping MemoriesBackendError variants to McpError must include MemoriesBackendError::InvalidMatchWindow, mapping it to McpError::invalid_params with the error's string representation.

Type: Struct/Args
Name: SearchArgs
Location: codex-rs/memories/mcp/src/server.rs
Description: The SearchArgs type (used to deserialize tool call arguments) must support the AllWithinLines match mode via its into_request() method, returning a SearchMemoriesRequest with match_mode set to SearchMatchMode::AllWithinLines { line_count } when the input specifies type "all_within_lines".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.