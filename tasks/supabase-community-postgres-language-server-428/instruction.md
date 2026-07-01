Fix the handling of multiple simultaneous document changes in the Postgres language server to ensure that all changes are applied at their correct positions. This will prevent false error diagnostics when editors send multiple text changes in a single notification.

*   Implement the `did_change` function in `crates/pgt_lsp/src/handlers/text_document.rs`:
    *   Ensure it handles `textDocument/didChange` notifications by correctly applying all content changes in `params.content_changes`.
    *   Update the line index between successive changes so each change's range is resolved against the correct document state.
    *   Ensure no diagnostics are published if the resulting SQL document is valid and references existing tables.

*   Implement the `apply_document_changes` function in `crates/pgt_lsp/src/utils.rs`:
    *   Apply a sequence of LSP content change events to a string and return the updated text.
    *   Handle both full-document replacements (range = None) and range-based edits.
    *   Rebuild the line index as needed after each change to maintain correct positions.

*   Update the `change_document` method in `crates/pgt_lsp/tests/server.rs`:
    *   Accept a version number and a `Vec<TextDocumentContentChangeEvent>`.
    *   Forward them as a `textDocument/didChange` notification using the default document URL.

*   Update the `change_named_document` method in `crates/pgt_lsp/tests/server.rs`:
    *   Accept a URL, a version number, and a `Vec<TextDocumentContentChangeEvent>`.
    *   Forward them as a `textDocument/didChange` notification to the server.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.