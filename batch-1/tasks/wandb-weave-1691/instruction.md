Implement an expression-based query filtering capability for the call trace server. Update the system to allow filtering based on dynamic input and output field values, and add a new endpoint to return the count of matching calls. Ensure the expression language supports various operators and type conversions.

*   Update `CallsQueryReq` in `weave/trace_server/trace_server_interface.py`:
    *   Add an optional field: `query: Optional[Query] = None`.

*   Implement the expression language in `weave/trace_server/interface/query.py`:
    *   Define the `Query` class with `expr_: "Operation" = Field(alias="$expr")`.
    *   Define operation classes for:
        *   `LiteralOperation` with `literal_: Union[str, int, float, bool, dict[str, "LiteralOperation"], list["LiteralOperation"]] = Field(alias="$literal")`.
        *   `GetFieldOperator` with `get_field_: str = Field(alias="$getField")`.
        *   `ConvertOperation` with `convert_: "ConvertSpec" = Field(alias="$convert")`.
        *   `AndOperation` with `and_: List["Operand"] = Field(alias="$and")`.
        *   `OrOperation` with `or_: List["Operand"] = Field(alias="$or")`.
        *   `NotOperation` with `not_: Tuple["Operand"] = Field(alias="$not")`.
        *   `EqOperation` with `eq_: Tuple["Operand", "Operand"] = Field(alias="$eq")`.
        *   `GtOperation` with `gt_: Tuple["Operand", "Operand"] = Field(alias="$gt")`.
        *   `GteOperation` with `gte_: Tuple["Operand", "Operand"] = Field(alias="$gte")`.
        *   `ContainsOperation` with `contains_: "ContainsSpec" = Field(alias="$contains")`.
    *   Define type aliases:
        *   `Operation = Union[AndOperation, OrOperation, NotOperation, EqOperation, GtOperation, GteOperation, ContainsOperation]`.
        *   `Operand = Union[LiteralOperation, GetFieldOperator, ConvertOperation, "Operation"]`.
    *   Ensure all models call `model_rebuild()` after definition.

*   Add new classes in `weave/trace_server/trace_server_interface.py`:
    *   `CallsQueryStatsReq` with fields: `project_id: str`, `filter: Optional[_CallsFilter] = None`, `query: Optional[Query] = None`.
    *   `CallsQueryStatsRes` with field: `count: int`.
    *   Implement `calls_query_stats` method in `TraceServerInterface` with signature: `calls_query_stats(self, req: CallsQueryStatsReq) -> CallsQueryStatsRes`.

*   Modify `weave/trace_server/sqlite_trace_server.py`:
    *   Implement `calls_query_stats` in `SqliteTraceServer` to delegate to `calls_query` and count results.
    *   Update `calls_query` to handle `query.expr_` when `req.query` is provided, applying it as an additional filter condition.

*   Ensure the query expression supports:
    *   Comparison operators: $eq, $gt, $gte.
    *   Logical operators: $not, $and, $or.
    *   $literal operand for constant values.
    *   $getField operand for accessing nested fields.
    *   $convert operand for type conversions.
    *   $contains operator for substring matching with optional case insensitivity.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.