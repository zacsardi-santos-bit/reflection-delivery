I'm working on adding cursor-based pagination to the task instances listing endpoint in our Airflow FastAPI application.

*   A new module at airflow/api_fastapi/common/cursors.py must expose three functions: encode_cursor, decode_cursor, and apply_cursor_filter.

*   encode_cursor must accept a row object and a SortParam instance, extract the values of the sort columns (including the 'id' tiebreaker) from the row, serialize them via msgpack, and return the result base64url-encoded (without padding) as a string.

*   encode_cursor must work even when SortParam.to_orm() has not been called beforehand — column resolution must happen lazily.

*   decode_cursor must accept a base64url-encoded token string and return the deserialized Python list. If the token is invalid base64 or cannot be decoded as msgpack, it must raise an HTTPException whose detail contains the text 'Invalid cursor token'.

*   decode_cursor must raise an HTTPException whose detail contains the text 'Invalid cursor token structure' when the decoded msgpack value is not a Python list.

*   apply_cursor_filter must accept a SQLAlchemy select statement, a cursor token string, and a SortParam instance, and return the statement with a cursor-based WHERE clause appended. If the number of values in the decoded token does not match the number of resolved sort columns, it must raise an HTTPException whose detail contains the text 'does not match'.

*   apply_cursor_filter must use a greater-than comparison ('>') in the generated SQL when the sort direction is ascending, and a less-than comparison ('<') when the sort direction is descending.

*   SortParam must expose a get_resolved_columns() method that returns a list of tuples, each containing (column_name: str, column_object, is_descending: bool). The method must automatically append the 'id' column as a tiebreaker when 'id' is not already among the sort columns. It must not duplicate 'id' when 'id' is explicitly present in the sort columns.

*   SortParam.get_resolved_columns() must set is_descending=False for ascending sort columns and is_descending=True for descending sort columns (those whose sort key is prefixed with '-'). When all columns are descending, the 'id' tiebreaker appended automatically must also have is_descending=True.

*   The GET /dags/~/dagRuns/~/taskInstances endpoint must accept an optional 'cursor' query parameter. When the cursor parameter is present, the response body must include 'next_cursor' and 'previous_cursor' string fields (nullable), and 'total_entries' must be null.

*   When cursor-based pagination is active and there are more results beyond the current page, 'next_cursor' must be a non-null token. When the current page is the first page, 'previous_cursor' must be null. When the current page is the last page, 'next_cursor' must be null and 'previous_cursor' must be non-null if previous pages exist.

*   An empty string cursor value must be treated as the first page in cursor pagination mode.

*   When an invalid cursor token is supplied to the task instances endpoint, the endpoint must return HTTP 400.

*   The task instances collection response body must always include 'next_cursor' and 'previous_cursor' fields even when cursor pagination is not active (they must be null in that case).


*   Interface details: Type: Function
Name: encode_cursor
Location: airflow-core/src/airflow/api_fastapi/common/cursors.py
Signature: encode_cursor(row: Any, sort_param: SortParam) -> str
Description: Reads the values of each resolved sort column (plus the 'id' tiebreaker) from the row object, serializes the list with msgpack, and returns the result as a base64url-encoded string without padding. Works even if SortParam.to_orm() has not been called beforehand (lazy column resolution).

Type: Function
Name: decode_cursor
Location: airflow-core/src/airflow/api_fastapi/common/cursors.py
Signature: decode_cursor(token: str) -> list
Description: Decodes a base64url-encoded msgpack cursor token back to a Python list. Raises fastapi.HTTPException (detail contains "Invalid cursor token") if the token is not valid base64 or cannot be decoded as msgpack. Raises fastapi.HTTPException (detail contains "Invalid cursor token structure") if the decoded value is not a Python list.

Type: Function
Name: apply_cursor_filter
Location: airflow-core/src/airflow/api_fastapi/common/cursors.py
Signature: apply_cursor_filter(stmt: Select, token: str, sort_param: SortParam) -> Select
Description: Decodes the cursor token and appends a WHERE clause to the SQLAlchemy select statement that positions the query after (or before) the encoded row. Uses '>' for ascending columns and '<' for descending columns. Raises fastapi.HTTPException (detail contains "does not match") if the number of values in the decoded token does not equal the number of resolved sort columns.

Type: Method
Name: get_resolved_columns
Location: airflow-core/src/airflow/api_fastapi/common/parameters.py
Signature: get_resolved_columns() -> list[tuple[str, Any, bool]]
Description: Method on the existing SortParam class. Returns a list of (column_name, column_object, is_descending) tuples for the current sort configuration. Automatically appends 'id' as a tiebreaker column when 'id' is not already in the sort list; does not duplicate 'id' when it is already present. is_descending is False for ascending columns and True for descending columns (those with a '-' prefix). The tiebreaker 'id' column inherits the same is_descending value as the other columns. Column resolution is lazy — this method does not require SortParam.to_orm() to have been called first.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.