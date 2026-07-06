Implement code actions in the Noir language server to assist developers in resolving unrecognized identifiers by either adding import statements or qualifying references with their full paths. Ensure these actions are available for both struct types and modules within nested submodule hierarchies.

*   Implement the `on_code_action_request` function:
    *   Location: `tooling/lsp/src/requests/code_action.rs`
    *   Signature: `async fn on_code_action_request(state: &mut LspState, params: CodeActionParams) -> Result<Option<CodeActionResponse>, ResponseError>`
    *   Purpose: Return code actions for unresolved struct types or module identifiers at the cursor position to either qualify the identifier or add an import statement.

*   Ensure the file `tooling/lsp/src/requests/code_action.rs`:
    *   Exists at the specified path.
    *   Declares the tests submodule to include `tooling/lsp/src/requests/code_action/tests.rs`.

*   Code actions for unresolved struct type identifiers:
    *   Return a code action titled 'Qualify as {full::path::TypeName}'.
        *   Produces a single text edit replacing the bare identifier with the fully qualified path on the same line.
    *   Return a code action titled 'Import {full::path::TypeName}'.
        *   Produces a single text edit inserting 'use {full::path::TypeName};' at the beginning of the file.

*   Code actions for unresolved module identifiers:
    *   Return a code action titled 'Qualify as {full::path::module_name}'.
        *   Replaces the bare module name with its fully qualified path.
    *   Return a code action titled 'Import {full::path::module_name}'.
        *   Inserts 'use {full::path::module_name};' at the beginning of the file.

*   Code actions must be returned as `CodeAction` objects:
    *   Include a title field matching the specified formats.
    *   Include an edit field (WorkspaceEdit) with a changes map containing exactly one entry.
    *   Each entry must contain exactly one `TextEdit`.
    *   TextEdits must span only a single line (start line and end line must be equal).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.