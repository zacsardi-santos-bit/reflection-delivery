I'm working on adding support to the language server so it can format files that are outside the current workspace.

*   The language server must correctly format TypeScript files that are located outside the configured workspace directory. When the workspace is initialized with a specific directory, files in parent or sibling directories must also receive formatting edits.

*   When formatting a TypeScript file with content 'const x=1', the language server must produce the formatted output 'const x = 1;' regardless of whether the file is inside or outside the workspace root.

*   WorkerManager must be a public type exported from the language server crate, constructible via WorkerManager::new(builder: Arc<dyn ToolBuilder>) -> WorkerManager for standard workspace mode.

*   WorkerManager must also be constructible via WorkerManager::new_with_mode(builder: Arc<dyn ToolBuilder>, mode: ManagerMode) -> WorkerManager to specify an explicit operating mode.

*   ManagerMode must be a public enum exported from the language server crate. It must include a DynamicWithWorkspaces variant that wraps a Box<RwLock<WorkspaceWorker>>.

*   WorkspaceWorker must be a public struct exported from the language server crate, constructible via WorkspaceWorker::new(uri: Uri, builder: Arc<dyn ToolBuilder>, mode: DiagnosticMode) -> WorkspaceWorker.

*   Backend::new must accept a WorkerManager as its third parameter. The previous signature accepting Arc<dyn ToolBuilder> directly is no longer valid.

*   In DynamicWithWorkspaces mode, pull diagnostic requests for files outside the workspace must succeed and return a full diagnostic report (kind: 'full') containing diagnostic items.

*   In DynamicWithWorkspaces mode, file watcher registration must only register watchers scoped to specific workspace URIs, not a global filesystem watcher.


*   Interface details: Type: Struct
Name: WorkerManager
Location: crates/oxc_language_server/src/ (exported from crate root)
Description: Manages workspace workers for the language server. Must be publicly exported from the oxc_language_server crate.
Signature:
  WorkerManager::new(builder: Arc<dyn ToolBuilder>) -> WorkerManager
  WorkerManager::new_with_mode(builder: Arc<dyn ToolBuilder>, mode: ManagerMode) -> WorkerManager

Type: Enum
Name: ManagerMode
Location: crates/oxc_language_server/src/ (exported from crate root)
Description: Specifies the operating mode for a WorkerManager. Must be publicly exported from the oxc_language_server crate.
Variants:
  DynamicWithWorkspaces(Box<tokio::sync::RwLock<WorkspaceWorker>>) — enables dynamic handling of files inside and outside the workspace

Type: Struct
Name: WorkspaceWorker
Location: crates/oxc_language_server/src/ (exported from crate root)
Description: Represents a worker for a specific workspace URI. Must be publicly exported from the oxc_language_server crate.
Signature:
  WorkspaceWorker::new(uri: tower_lsp_server::ls_types::Uri, builder: Arc<dyn ToolBuilder>, mode: DiagnosticMode) -> WorkspaceWorker

Type: Function (updated signature)
Name: Backend::new
Location: crates/oxc_language_server/src/backend.rs (or backend/mod.rs)
Description: The Backend constructor. Its third parameter must change from Arc<dyn ToolBuilder> to WorkerManager.
Signature: Backend::new(client: Client, server_info: ServerInfo, manager: WorkerManager) -> Backend


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.