I'm working on cursor-based pagination in an API that supports sorting by aliased column names — where the user-facing sort field name differs from the underlying database column name.

*   The `SortParam` class must expose a `row_value(row, name)` method that extracts a sort-key value from a result row object.

*   When `to_replace` contains `name` mapped to a plain string (e.g. `{'dag_run_id': 'run_id'}`), `row_value` must return `getattr(row, replacement_string)` — i.e. look up the underlying attribute name, not the user-facing alias.

*   When `to_replace` contains `name` mapped to a non-string value (a SQLAlchemy Column object), `row_value` must raise `NotImplementedError` with an error message matching the pattern `column-form ``to_replace```.

*   When `name` has no entry in `to_replace` (or `to_replace` is absent), `row_value` must return `getattr(row, name)`.

*   The `get_resolved_columns()` method must not append the primary key a second time when an alias in `to_replace` already maps to a string whose value equals the primary key column's `.key` attribute.

*   For a `SortParam` configured with `{'import_error_id': 'id'}` on a model whose primary key column key is `id`, calling `set_value(['import_error_id'])` and then `get_resolved_columns()` must return exactly one entry with attr_name `import_error_id`.

*   The resolved (attr_name, column, is_desc) tuples returned by `get_resolved_columns()` must use the user-facing alias name as the `attr_name` (not the underlying column name), so that `row_value` can be called with those same names to recover the correct row values.


*   Interface details: Type: Class
Name: SortParam
Location: airflow-core/src/airflow/api_fastapi/common/parameters.py
Description: Manages sort parameters for API query endpoints, including alias resolution and primary-key tiebreaker logic.

Type: Method
Name: row_value
Location: airflow-core/src/airflow/api_fastapi/common/parameters.py
Signature: row_value(self, row: Any, name: str) -> Any
Description: Extract the sort-key value for `name` from a result row. If `to_replace` contains `name` mapped to a plain string, returns `getattr(row, that_string)`. If `to_replace` contains `name` mapped to a non-string (Column object), raises `NotImplementedError` with a message containing the text `column-form ``to_replace```. Otherwise returns `getattr(row, name)`.

Type: Method
Name: get_resolved_columns
Location: airflow-core/src/airflow/api_fastapi/common/parameters.py
Signature: get_resolved_columns(self) -> list[tuple[str, ColumnElement, bool]]
Description: Returns resolved sort columns as (attr_name, column_element, is_descending) tuples. The attr_name is the user-facing alias name (not the underlying column name). Must not append the primary key a second time when any already-resolved column's `.key` attribute matches the primary key name.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.