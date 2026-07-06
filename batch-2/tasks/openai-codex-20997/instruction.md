I'm working on a memory file search feature and need to extend it with two capabilities that are currently missing.

*   The SearchMemoriesRequest struct must include two new fields: context_lines (an unsigned integer representing the number of surrounding lines to include with each match) and case_sensitive (a boolean controlling whether matching is case-sensitive).

*   The MemorySearchMatch struct must replace the existing single-line field with two new fields: start_line_number (a 1-based integer indicating the first line of the content window) and content (a string containing the match line and any surrounding context lines joined by newline characters, with no trailing newline).

*   When context_lines is 0, each match result must contain only the matched line as content, and start_line_number must equal line_number.

*   When context_lines is greater than 0, each match result's content must include up to context_lines lines before and after the matched line, joined by newline characters. The start_line_number must reflect the 1-based line number of the first included line. Context is bounded by file boundaries — no lines before the first line or after the last line of the file are included.

*   When case_sensitive is true, only lines that contain the query string in the exact same capitalization are returned as matches.

*   When case_sensitive is false, lines are matched regardless of capitalization — all case variants of the query string must be found.

*   A cursor value that points past the end of available search results must be rejected and return a MemoriesBackendError::InvalidCursor error.


*   Interface details: Type: Struct
Name: SearchMemoriesRequest
Location: codex-rs/memories/mcp/src/backend.rs
Description: Request struct for searching memory files. Must include the following fields:
  - query: String
  - path: Option<String>
  - cursor: Option<String>
  - context_lines: usize  (NEW — number of surrounding lines to include with each match)
  - case_sensitive: bool  (NEW — whether matching is case-sensitive)
  - max_results: usize

Type: Struct
Name: MemorySearchMatch
Location: codex-rs/memories/mcp/src/backend.rs
Description: Represents a single search result. The existing `line: String` field must be replaced with two new fields:
  - path: String          (existing)
  - line_number: usize    (existing — 1-based line number of the matched line)
  - start_line_number: usize  (NEW — 1-based line number of the first line in the content window)
  - content: String       (NEW — the match line plus surrounding context lines joined by "\n", no trailing newline)

Type: Constant
Name: DEFAULT_SEARCH_MAX_RESULTS
Location: codex-rs/memories/mcp/src/backend.rs
Description: Default maximum number of search results to return per request. Already defined; used when constructing SearchMemoriesRequest in tests.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.