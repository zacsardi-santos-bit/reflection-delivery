I'm running into test failures after upgrading to a newer major version of the dataframe library.

*   The get_field_type function must classify any timedelta column as semantic type 'string' by matching the dtype family prefix rather than an exact string, so that both 'timedelta64[ns]' (pandas 2.x) and 'timedelta64[us]' (pandas 3.x) — and any other timedelta resolution — are all correctly classified.

*   The get_field_types function must return field type tuples that are valid for both pandas 2.x and pandas 3.x: string columns may have dtype_str 'object' or 'str'; datetime columns may have dtype_str 'datetime64[ns]' or 'datetime64[us]'; timedelta columns may have dtype_str 'timedelta64[ns]' or 'timedelta64[us]'.

*   The get_row_headers function must handle a timedelta-typed DataFrame index by returning semantic type 'string' paired with the actual dtype string, regardless of whether the time resolution is nanoseconds or microseconds.

*   The get_row_headers function must handle a datetime-typed DataFrame index by returning semantic type 'datetime' paired with the actual dtype string for both 'datetime64[ns]' and 'datetime64[us]' resolutions.

*   The _get_row_headers utility function must handle multi-index DataFrames where string-typed levels report either 'object' (pandas 2.x) or 'str' (pandas 3.x) as their dtype.

*   When converting a DataFrame to JSON bytes, null or missing timedelta/datetime values must serialize as JSON null rather than as a bare NaN or as the string 'NaT'.

*   The dataframe component's column metadata (the 'columns' argument) must accept 'str' as a valid dtype string for string-typed columns in addition to 'object', to support pandas 3.x behavior.

*   When searching a DataFrame for the string 'nan', the result row count must be either 0 or 4 depending on the pandas version: 4 for pandas 2.x with object dtype (where NaN casts to 'nan'), or 0 for pandas 3.x with StringDtype (where NaN becomes pd.NA and does not match).


*   Interface details: Type: Function
Name: get_field_type
Location: marimo/_plugins/ui/_impl/tables/pandas_table.py
Signature: get_field_type(name: str) -> tuple[str, str]
Description: Returns a 2-tuple of (semantic_type, dtype_str) for the named column. For timedelta columns, must return ("string", dtype_str) where dtype_str is the actual dtype string (e.g., "timedelta64[ns]", "timedelta64[us]"). The match for timedelta must be based on the type family (i.e., any dtype string that starts with "timedelta"), not a single exact string.

Type: Function
Name: get_field_types
Location: marimo/_plugins/ui/_impl/tables/pandas_table.py
Signature: get_field_types() -> list[tuple[str, tuple[str, str]]]
Description: Returns a list of (column_name, (semantic_type, dtype_str)) tuples for all columns. Timedelta columns must be returned with semantic_type "string" and the actual dtype string. String columns may return dtype_str "object" (pandas 2.x) or "str" (pandas 3.x). Datetime columns may return dtype_str "datetime64[ns]" (pandas 2.x) or "datetime64[us]" (pandas 3.x).

Type: Function
Name: get_row_headers
Location: marimo/_plugins/ui/_impl/tables/pandas_table.py
Signature: get_row_headers() -> list[tuple[str, tuple[str, str]]]
Description: Returns row header metadata for the DataFrame index. For a timedelta index, returns a list containing a tuple where the second element is ("string", dtype_str) and dtype_str matches any timedelta resolution (e.g., "timedelta64[ns]" or "timedelta64[us]"). For a datetime index, returns ("datetime", dtype_str) with dtype_str matching any datetime resolution.

Type: Function
Name: _get_row_headers
Location: marimo/_plugins/ui/_impl/utils/dataframe_utils.py
Signature: _get_row_headers(df: Any) -> list[tuple[str, tuple[str, str]]]
Description: Utility function returning row header metadata for a DataFrame. For multi-index DataFrames with string levels, dtype_str may be "object" (pandas 2.x) or "str" (pandas 3.x).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.