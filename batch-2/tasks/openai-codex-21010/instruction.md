I'm working on a memories backend and need to improve its error reporting for missing paths.

*   The MemoriesBackendError enum in codex-rs/memories/mcp/src/backend.rs must include a NotFound { path: String } variant, representing the case where the requested memory path does not exist on disk.

*   When a list operation targets a scoped path that does not exist, it must return Err(MemoriesBackendError::NotFound { path }) rather than returning an empty Ok response.

*   When a read operation targets a file path that does not exist, it must return Err(MemoriesBackendError::NotFound { path }) rather than returning Err(MemoriesBackendError::NotFile { path }).

*   When a search operation targets a scoped path that does not exist, it must return Err(MemoriesBackendError::NotFound { path }) rather than returning an empty Ok response.

*   Path resolution must validate each path component in order; if any resolved component is a symbolic link (in an ancestor directory position), the operation must return Err(MemoriesBackendError::InvalidPath { .. }) rather than proceeding.

*   The NotFound variant must be treated as a client error in the MCP server error mapping, consistent with InvalidPath and InvalidCursor.


*   Interface details: Type: Enum Variant
Name: NotFound
Location: codex-rs/memories/mcp/src/backend.rs
Signature: NotFound { path: String }
Description: A variant of MemoriesBackendError that signals a requested path does not exist on disk. Must be added to the existing MemoriesBackendError enum alongside InvalidPath, InvalidCursor, and other variants.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.