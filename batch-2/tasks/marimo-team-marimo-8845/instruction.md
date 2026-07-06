I'm working on refactoring the code editing context in marimo so that it uses the existing notebook document model instead of managing its own internal state.

*   AsyncCodeModeContext must read the current NotebookDocument from the _current_document context variable during initialization. If _current_document holds None (not set), AsyncCodeModeContext must raise a RuntimeError.

*   The NotebookCellData class and the module-level _cell_names dictionary must be removed from marimo/_code_mode/_context.py. Cell state must be sourced exclusively from the NotebookDocument snapshot.

*   When a cell has no explicit name, NotebookCell.name must be an empty string (''), not None.

*   After executing a batch of operations, AsyncCodeModeContext must broadcast exactly one NotebookDocumentTransactionNotification containing a Transaction with an ops tuple. The legacy UpdateCellIdsNotification must no longer be sent for structural changes.

*   When a new cell is created, the transaction must include a create-cell op that serializes (via msgspec.to_builtins) to: {type: 'create-cell', cellId: <id>, code: <code>, name: <name or ''>, config: {column: <value>, disabled: <bool>, hide_code: <bool>}, before: null, after: null}.

*   When a cell is deleted, the transaction must include a delete-cell op that serializes to: {type: 'delete-cell', cellId: <id>}.

*   When an existing cell's code is changed, the transaction must include a set-code op that serializes to: {type: 'set-code', cellId: <id>, code: <new_code>}.

*   When an existing cell's configuration is changed, the transaction must include a set-config op that serializes to: {type: 'set-config', cellId: <id>, column: <value>, disabled: <bool>, hideCode: <bool>}. The hide_code field must serialize as 'hideCode' (camelCase), not 'hide_code'.

*   Every non-empty transaction must end with a reorder-cells op that serializes to: {type: 'reorder-cells', cellIds: <tuple of all current cell IDs in notebook order>}. The cellIds value must be a tuple, not a list.

*   Within a transaction, delete-cell ops must appear before create-cell and set-code ops, which must appear before set-config ops, which must appear before the final reorder-cells op.

*   After operations are applied, the AsyncCodeModeContext must call self._document.apply(transaction) to update its local document snapshot so that subsequent cell name lookups reflect newly created or renamed cells.


*   Interface details: ## Interfaces Required by Tests

### Modified Class: AsyncCodeModeContext

Type: Class
Name: AsyncCodeModeContext
Location: marimo/_code_mode/_context.py
Description: Async context manager for batched notebook operations. The constructor must read the current NotebookDocument from the `_current_document` context variable. If `_current_document` is not set (returns None), the constructor must raise a RuntimeError. The document is stored as `self._document` and used as the source of truth for cell state.
Signature: __init__(self, kernel: Kernel, *, skip_validation: bool = False) -> None

Note: The `NotebookCellData` class previously defined in this module must be removed. The `_cell_names` module-level dict must also be removed. Cell data is now provided entirely through the NotebookDocument snapshot.

---

### Existing Symbol (already in codebase, must be used): NotebookCell

Type: Class
Name: NotebookCell
Location: marimo/_notebook/document.py
Description: Read-only representation of a single notebook cell. The `name` field is a plain `str` that defaults to `""` (empty string) when no name is provided. Unlike the old NotebookCellData, it must NOT use `None` for unnamed cells.
Fields: id (CellId_t), code (str), name (str, default ""), config (CellConfig)

---

### Existing Symbol (already in codebase, must be used): NotebookDocument

Type: Class
Name: NotebookDocument
Location: marimo/_notebook/document.py
Description: Ordered collection of NotebookCell instances representing the current notebook state.
Signature: __init__(self, cells: list[NotebookCell]) -> None
Key attributes/methods:
- `.cells` → list[NotebookCell]
- `.cell_ids` → list[CellId_t]
- iteration yields CellId_t values
- `.get(cell_id: CellId_t)` → NotebookCell | None
- `.apply(tx: Transaction) → None` (applies a transaction to the local snapshot)

---

### Existing Symbol (already in codebase, must be used): _current_document

Type: ContextVar
Name: _current_document
Location: marimo/_notebook/document.py
Description: A Python ContextVar holding the current NotebookDocument (or None if not set). Must be set via `_current_document.set(document)` before instantiating AsyncCodeModeContext. AsyncCodeModeContext reads this in its constructor.

---

### Existing Symbol (already in codebase, must be used): NotebookDocumentTransactionNotification

Type: Class
Name: NotebookDocumentTransactionNotification
Location: marimo/_messaging/notification.py
Description: Notification sent to the frontend describing all document changes in a single batch. Replaces the old UpdateCellIdsNotification and UpdateCellCodesNotification for structural and code updates.
Attributes:
- `transaction: Transaction` — the transaction containing all ops for this batch

---

### Existing Symbol (already in codebase, must be used): Transaction

Type: Class/Struct
Name: Transaction
Location: marimo/_notebook/ops.py
Description: Container for a sequence of document operations.
Attributes:
- `ops: tuple[Op, ...]` — the ordered list of ops
- `source: str` — origin label (e.g. "kernel")

---

### Existing Op Types (already in codebase, must be used): Op classes

Location: marimo/_notebook/ops.py

All op classes must serialize (via msgspec.to_builtins) to the following exact shapes:

**CreateCell** — emitted for newly created cells:
```
{
  "type": "create-cell",
  "cellId": "<cell_id>",
  "code": "<code>",
  "name": "<name or empty string>",
  "config": {"column": <int or None>, "disabled": <bool>, "hide_code": <bool>},
  "before": <cell_id or None>,
  "after": <cell_id or None>
}
```

**DeleteCell** — emitted when a cell is removed:
```
{"type": "delete-cell", "cellId": "<cell_id>"}
```

**SetCode** — emitted when an existing cell's code is changed:
```
{"type": "set-code", "cellId": "<cell_id>", "code": "<new_code>"}
```

**SetConfig** — emitted when an existing cell's config is changed (note camelCase `hideCode`):
```
{
  "type": "set-config",
  "cellId": "<cell_id>",
  "column": <int or None>,
  "disabled": <bool>,
  "hideCode": <bool>
}
```
Important: The `hide_code` field of `SetConfig` serializes as `hideCode` (camelCase), not `hide_code`.

**ReorderCells** — always the last op in any non-empty transaction, describes final cell ordering:
```
{"type": "reorder-cells", "cellIds": (<tuple of cell_ids in order>)}
```
Note: `cellIds` serializes as a Python tuple, not a list.

---

### Op Ordering Within a Transaction

Within a single transaction, ops must appear in this order:
1. `delete-cell` ops (cells removed)
2. `create-cell` ops (new cells) or `set-code` ops (code changes to existing cells)
3. `set-config` ops (config-only changes to existing cells)
4. One `reorder-cells` op (always last)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.