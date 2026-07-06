I'm working on replacing the existing notebook change notification system with a structured, transaction-based protocol.

*   A new module must provide a NotebookCell data type with fields: id (string cell identifier), code (string source code), name (string, defaults to empty string), and config (cell configuration object with column, disabled, and hide_code fields).

*   The same module must provide a NotebookDocument type that is constructed from a list of NotebookCell instances, representing the complete ordered document state.

*   The same module must provide a context variable _current_document that supports .set(NotebookDocument(...)) to establish the current notebook document before constructing an AsyncCodeModeContext.

*   AsyncCodeModeContext must read its initial document state from the _current_document context variable rather than deriving it from the kernel directly; tests must set _current_document before constructing the context.

*   When cells are added, the system must emit a NotebookDocumentTransactionNotification whose transaction contains a create-cell op with fields: type='create-cell', cellId (str), code (str), name (str), config (dict with column, disabled, hide_code), before (str or None), after (str or None).

*   When cells are deleted, the transaction must contain a delete-cell op with fields: type='delete-cell', cellId (str).

*   When cell code is updated, the transaction must contain a set-code op with fields: type='set-code', cellId (str), code (str).

*   When cell configuration is updated, the transaction must contain a set-config op with fields: type='set-config', cellId (str), column (int or None), disabled (bool), hideCode (bool) — note the camelCase field name hideCode.

*   Every transaction notification must also include a reorder-cells op with fields: type='reorder-cells', cellIds (tuple/sequence of all current cell IDs in order).

*   NotebookDocumentTransactionNotification must be serializable via msgspec.to_builtins(), producing plain dicts whose 'type' field matches the op type strings listed above.

*   The cell name field must default to an empty string ('') rather than None when no name has been explicitly assigned.

*   UpdateCellIdsNotification is no longer emitted for structural changes; NotebookDocumentTransactionNotification replaces it for cell ordering and creation/deletion events.


*   Interface details: Type: Class
Name: NotebookCell
Location: marimo/_notebook/document.py
Description: Represents a single notebook cell with its identifier, source code, name, and configuration. Fields: id (str/CellId_t), code (str), name (str, defaults to ""), config (cell configuration object with column, disabled, hide_code fields).
Signature: NotebookCell(id: str, code: str, name: str, config: object)

Type: Class
Name: NotebookDocument
Location: marimo/_notebook/document.py
Description: Represents the full ordered notebook document as a collection of NotebookCell instances.
Signature: NotebookDocument(cells: list[NotebookCell])

Type: Variable
Name: _current_document
Location: marimo/_notebook/document.py
Description: A context variable (ContextVar or equivalent) holding the current NotebookDocument. Supports .set(NotebookDocument(...)) to establish document state before constructing an AsyncCodeModeContext. Tests call _current_document.set(NotebookDocument([...])) before creating AsyncCodeModeContext.

Type: Class
Name: NotebookDocumentTransactionNotification
Location: marimo/_messaging/notification.py
Description: A notification object emitted when notebook structural changes occur. Has a .transaction attribute whose .ops field is a sequence of operation objects serializable via msgspec.to_builtins(). The serialized ops are plain dicts with a 'type' field. Supported op types and their serialized shapes:
  - create-cell: {type, cellId, code, name, config: {column, disabled, hide_code}, before, after}
  - delete-cell: {type, cellId}
  - set-code: {type, cellId, code}
  - set-config: {type, cellId, column, disabled, hideCode}  (note: camelCase hideCode)
  - reorder-cells: {type, cellIds}  (cellIds is a tuple/sequence of all current cell IDs in order)
Each transaction emitted by the context must include a reorder-cells op in addition to the structural ops.

Type: Class
Name: AsyncCodeModeContext
Location: marimo/_code_mode/_context.py
Description: Context manager for programmatic notebook edits. Must be constructed after _current_document has been set with the current notebook document state. Emits NotebookDocumentTransactionNotification (not UpdateCellIdsNotification) when cells are created, deleted, updated, or reordered.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.