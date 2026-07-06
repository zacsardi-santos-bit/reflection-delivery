I'm working on the file reload path in marimo and running into a couple of problems.

*   AppFileManager.reload() must return a 2-tuple (transaction, changed_cell_ids) where the first element is a Transaction object describing the diff and the second element is a set of CellId_t values for cells that were added, deleted, or had their code/name/config changed.

*   After AppFileManager.reload() completes, app.cell_manager must be the same Python object as before the reload (object identity is preserved, not just equality). The same identity requirement applies to app.cell_manager.document and to app.cell_manager._compiled_cells (the internal dict must not be replaced with a new one).

*   Each call to AppFileManager.reload() that processes file changes must advance app.cell_manager.document.version monotonically — each successive reload must produce a strictly greater version than the one before it.

*   A function _build_transaction must exist in marimo/_session/notebook/file_manager.py. It must accept two keyword-only arguments, prev and new, both CellManager instances. It must return a 2-tuple (transaction, changed_cell_ids) where transaction is an unstamped Transaction and changed_cell_ids is a set[CellId_t] covering creates, deletes, and content/config/name changes — but not reorder-only cells.

*   The old _build_reload_transaction function must no longer be importable from marimo._session.file_change_handler.

*   After FileChangeCoordinator.handle_change() processes a file change, app.cell_manager.document must still be the same Python object it was before the call (object identity preserved).

*   When FileChangeCoordinator.handle_change() is called with a file path that does not match the session's current file, the returned FileChangeResult must have a truthy error field (non-None, non-empty string).

*   When FileChangeCoordinator.handle_change() processes a config-only change, the returned FileChangeResult must have a truthy changed_cell_ids field.


*   Interface details: Type: Function
Name: _build_transaction
Location: marimo/_session/notebook/file_manager.py
Signature: _build_transaction(*, prev: CellManager, new: CellManager) -> tuple[Transaction, set[CellId_t]]
Description: Diffs two CellManager instances, returning a 2-tuple of (transaction, changed_cell_ids). Both arguments are keyword-only. The transaction is an unstamped Transaction object. changed_cell_ids covers cells whose code, name, or config changed, plus all created and deleted cells — reorder-only cells are excluded.

Type: Method
Name: reload
Location: marimo/_session/notebook/file_manager.py
Class: AppFileManager
Signature: reload(self) -> tuple[Transaction, set[CellId_t]]
Description: Reloads the app from disk. Returns a 2-tuple (transaction, changed_cell_ids). After reload, app.cell_manager must be the same Python object as before (object identity preserved), app.cell_manager.document must be the same object, and app.cell_manager._compiled_cells must be the same dict object. The document version (app.cell_manager.document.version) must advance monotonically with each reload call.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.