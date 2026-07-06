I'm working on a language server codebase where the tool interface currently passes a file's location and content as separate parameters to every diagnostic and formatting method.

*   A new public struct named `TextDocument` must be defined in `crates/oxc_language_server/src/lib.rs` and re-exported from the crate. It must have a lifetime parameter and three public fields: `uri` (a borrowed reference to a URI), `language_id` (a `LanguageId` value), and `text` (an `Option<String>`).

*   The `TextDocument` struct must provide a public constructor `new(uri: &'a Uri, language_id: LanguageId, text: Option<String>) -> Self` that initializes all three fields.

*   The `Tool` trait's `run_diagnostic` default method signature must be updated from `(&self, uri: &Uri, content: Option<&str>) -> DiagnosticResult` to `(&self, document: &TextDocument) -> DiagnosticResult`.

*   The `Tool` trait's `run_diagnostic_on_change` default method signature must be updated from `(&self, uri: &Uri, content: Option<&str>) -> DiagnosticResult` to `(&self, document: &TextDocument) -> DiagnosticResult`.

*   The `Tool` trait's `run_diagnostic_on_save` default method signature must be updated from `(&self, uri: &Uri, content: Option<&str>) -> DiagnosticResult` to `(&self, document: &TextDocument) -> DiagnosticResult`.

*   The `Tool` trait's `run_format` default method signature must be updated from `(&self, uri: &Uri, language_id: &LanguageId, content: Option<&str>) -> Result<Vec<TextEdit>, String>` to `(&self, document: &TextDocument) -> Result<Vec<TextEdit>, String>`.

*   All implementations of the `Tool` trait throughout the codebase (including in `ServerLinter` and `ServerFormatter`) must be updated to use the new `&TextDocument` parameter for `run_diagnostic`, `run_diagnostic_on_change`, `run_diagnostic_on_save`, and `run_format`.

*   The `WorkspaceWorker::run_diagnostic`, `WorkspaceWorker::run_diagnostic_on_change`, and `WorkspaceWorker::run_diagnostic_on_save` methods must be updated to accept `&TextDocument<'_>` instead of separate `uri: &Uri` and `content: Option<&str>` parameters.

*   The `WorkspaceWorker::format_file` method must be updated to accept `&TextDocument<'_>` instead of `uri: &Uri, language_id: &LanguageId, content: Option<&str>`.

*   The `LSPFileSystem::get` method (returning `Option<(LanguageId, String)>`) must be replaced or supplemented with a `get_document<'a>(&self, uri: &'a Uri) -> TextDocument<'a>` method that constructs a `TextDocument` from the stored file entry, using `LanguageId::default()` and `text: None` when the URI is not found.

*   All call sites in `backend.rs` and `worker.rs` that previously passed separate `uri` and `content` arguments to diagnostic and formatting methods must be updated to construct and pass a `TextDocument` instead.


*   Interface details: Type: Struct
Name: TextDocument
Location: crates/oxc_language_server/src/lib.rs
Description: A struct that bundles a file's URI reference, language ID, and optional text content into a single document value for use across the tool interface and worker layer. Must be re-exported from the crate root.
Signature:
  pub struct TextDocument<'a> {
      pub uri: &'a Uri,
      pub language_id: LanguageId,
      pub text: Option<String>,
  }
  pub fn new(uri: &'a Uri, language_id: LanguageId, text: Option<String>) -> Self

---

Type: Trait
Name: Tool
Location: crates/oxc_language_server/src/tool.rs
Description: Trait that language server tools implement. The following method signatures must be updated to accept &TextDocument instead of separate URI and content parameters:
  fn run_diagnostic(&self, document: &TextDocument) -> DiagnosticResult
  fn run_diagnostic_on_change(&self, document: &TextDocument) -> DiagnosticResult
  fn run_diagnostic_on_save(&self, document: &TextDocument) -> DiagnosticResult
  fn run_format(&self, document: &TextDocument) -> Result<Vec<TextEdit>, String>

---

Type: Method
Name: WorkspaceWorker::run_diagnostic
Location: crates/oxc_language_server/src/worker.rs
Description: Aggregates diagnostics across all registered tools for the given document. Signature must change from (uri: &Uri, content: Option<&str>) to (document: &TextDocument<'_>).
Signature: pub async fn run_diagnostic(&self, document: &TextDocument<'_>) -> Result<Vec<(Uri, Vec<Diagnostic>)>, String>

Type: Method
Name: WorkspaceWorker::run_diagnostic_on_change
Location: crates/oxc_language_server/src/worker.rs
Description: Aggregates on-change diagnostics across all registered tools for the given document.
Signature: pub async fn run_diagnostic_on_change(&self, document: &TextDocument<'_>) -> Result<Vec<(Uri, Vec<Diagnostic>)>, String>

Type: Method
Name: WorkspaceWorker::run_diagnostic_on_save
Location: crates/oxc_language_server/src/worker.rs
Description: Aggregates on-save diagnostics across all registered tools for the given document.
Signature: pub async fn run_diagnostic_on_save(&self, document: &TextDocument<'_>) -> Result<Vec<(Uri, Vec<Diagnostic>)>, String>

Type: Method
Name: WorkspaceWorker::format_file
Location: crates/oxc_language_server/src/worker.rs
Description: Runs formatting across all registered tools for the given document.
Signature: pub async fn format_file(&self, document: &TextDocument<'_>) -> Result<Vec<TextEdit>, String>

---

Type: Method
Name: LSPFileSystem::get_document
Location: crates/oxc_language_server/src/file_system.rs
Description: Returns a TextDocument for the given URI. If the URI is not found in the file system, returns a TextDocument with LanguageId::default() and text: None.
Signature: pub fn get_document<'a>(&self, uri: &'a Uri) -> TextDocument<'a>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.