I'm working on marimo's AI tooling and I need a new tool that exposes the notebook's cell dependency graph to the AI assistant.

*   The GetCellDependencyGraph tool's handle method must accept a GetCellDependencyGraphArgs argument and return a GetCellDependencyGraphOutput with status equal to 'success'.

*   GetCellDependencyGraphArgs must have fields: session_id (required), cell_id (optional, defaults to None), and depth (optional integer, defaults to None).

*   GetCellDependencyGraphOutput must have fields: cells (list of CellDependencyInfo), variable_owners (dict mapping variable names to sorted lists of cell ID strings), multiply_defined (list of variable names defined in more than one cell), and cycles (list of cycle info objects).

*   Each CellDependencyInfo must have fields: cell_id (str), cell_name (str), defs (list of VariableInfo sorted alphabetically by name), refs (sorted list of referenced variable name strings), parent_cell_ids (sorted list of cell ID strings), and child_cell_ids (sorted list of cell ID strings).

*   VariableInfo must have name (str) and kind (str) fields and must support equality comparison. The kind field must reflect the type of the variable (e.g., 'variable', 'function') as provided by the cell's variable_data.

*   When no cell_id is provided, handle must return all cells from the graph in notebook order (the order returned by the cell manager).

*   When cell_id and depth (an integer) are provided, handle must include only cells within that many hops (in the parent and child directions) from the specified cell. Cells beyond that hop limit must be excluded from the cells list.

*   When cell_id is provided with depth=None, handle must include the specified cell plus all of its transitive ancestors and descendants (full transitive closure).

*   When an invalid cell_id (one not present in the graph) is provided, handle must raise a ToolExecutionError with code equal to 'CELL_NOT_FOUND'.

*   The variable_owners field must always reflect the full global graph regardless of any cell_id or depth filtering applied to the cells list.

*   For an empty graph (no cells, no definitions), the result must have cells=[], variable_owners={}, multiply_defined=[], and cycles=[].

*   Both GetCellDependencyGraphArgs and GetCellDependencyGraphOutput must be importable from marimo._ai._tools.tools.dependency_graph and must be compatible with the project's msgspec/pydantic serialization hook used by other tool I/O classes.


*   Interface details: Type: Class
Name: GetCellDependencyGraph
Location: marimo/_ai/_tools/tools/dependency_graph.py
Description: AI tool that returns the cell dependency graph for a marimo notebook session.
Signature: handle(args: GetCellDependencyGraphArgs) -> GetCellDependencyGraphOutput

Type: Class
Name: GetCellDependencyGraphArgs
Location: marimo/_ai/_tools/tools/dependency_graph.py
Description: Arguments dataclass for GetCellDependencyGraph. Must be compatible with the project's msgspec/pydantic serialization hook.
Signature: GetCellDependencyGraphArgs(session_id: SessionId, cell_id: Optional[CellId_t] = None, depth: Optional[int] = None)

Type: Class
Name: GetCellDependencyGraphOutput
Location: marimo/_ai/_tools/tools/dependency_graph.py
Description: Output dataclass for GetCellDependencyGraph. Subclass of SuccessResult (status == "success"). Must be compatible with the project's msgspec/pydantic serialization hook.
Fields: cells: list[CellDependencyInfo], variable_owners: dict[str, list[str]], multiply_defined: list[str], cycles: list

Type: Class
Name: VariableInfo
Location: marimo/_ai/_tools/tools/dependency_graph.py
Description: Dataclass representing a single defined variable with its name and kind. Must support equality comparison.
Signature: VariableInfo(name: str, kind: str)

Type: Class
Name: CellDependencyInfo
Location: marimo/_ai/_tools/tools/dependency_graph.py
Description: Dataclass representing one cell's dependency information.
Fields: cell_id: str, cell_name: str, defs: list[VariableInfo], refs: list[str], parent_cell_ids: list[str], child_cell_ids: list[str]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.