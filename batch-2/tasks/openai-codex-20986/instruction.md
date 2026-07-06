I'm working on the memories backend in the Codex Rust codebase, and I need to add support for reading a memory file starting from a specific line rather than always from the beginning.

*   The ReadMemoryRequest struct must include a new field named line_offset of type usize, representing a 1-indexed line number where reading should begin.

*   The ReadMemoryResponse struct must include a new field named start_line_number of type usize, which reflects the line offset from the request.

*   The MemoriesBackendError enum must include a new variant named InvalidLineOffset. This variant is returned when the provided line_offset is 0 (since valid offsets are 1-indexed).

*   The MemoriesBackendError enum must include a new variant named LineOffsetExceedsFileLength. This variant is returned when the provided line_offset is greater than the total number of lines in the file.

*   When line_offset is 1, reading must return the entire file content starting from the beginning, with start_line_number set to 1.

*   When line_offset is a valid value greater than 1 and within the file's line count, reading must return only the content from that line onward (inclusive), with start_line_number set to the line_offset value.

*   When line_offset is 0, the read operation must return Err(MemoriesBackendError::InvalidLineOffset) without reading the file.

*   When line_offset exceeds the number of lines in the file (e.g., line_offset is 3 but the file has only 1 line), the read operation must return Err(MemoriesBackendError::LineOffsetExceedsFileLength).

*   The InvalidLineOffset and LineOffsetExceedsFileLength error variants must be handled in the MCP server error mapping, converting them to invalid parameter errors (same category as InvalidPath, NotFile, and EmptyQuery).

*   The JSON schema for the read tool input must include line_offset as an optional integer field with a minimum value of 1. The JSON schema for the read tool output must include start_line_number as a required integer field.


*   Interface details: Type: Struct field
Name: line_offset
Location: codex-rs/memories/mcp/src/backend.rs
Signature: line_offset: usize
Description: Field added to the ReadMemoryRequest struct. Represents the 1-indexed line number from which reading should begin. A value of 1 means start from the beginning.

Type: Struct field
Name: start_line_number
Location: codex-rs/memories/mcp/src/backend.rs
Signature: start_line_number: usize
Description: Field added to the ReadMemoryResponse struct. Echoes back the line offset that was used, indicating which line the returned content starts from.

Type: Enum variant
Name: InvalidLineOffset
Location: codex-rs/memories/mcp/src/backend.rs
Signature: MemoriesBackendError::InvalidLineOffset
Description: New unit variant of the MemoriesBackendError enum. Returned when the provided line_offset is 0, which is not a valid 1-indexed line number.

Type: Enum variant
Name: LineOffsetExceedsFileLength
Location: codex-rs/memories/mcp/src/backend.rs
Signature: MemoriesBackendError::LineOffsetExceedsFileLength
Description: New unit variant of the MemoriesBackendError enum. Returned when the provided line_offset is greater than the total number of lines in the file.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.