Implement two utility modules for a feedback system on the weave trace server. The first module normalizes emoji skin tones, and the second provides a lightweight SQL generation layer compatible with both ClickHouse and SQLite databases.

*   Implement emoji normalization functions in `weave/trace_server/emoji_util.py`:
    *   `detone_shortcode(shortcode: str) -> str`: Strip skin-tone suffixes from emoji shortcodes.
        *   Return unchanged if no tone is present.
        *   Handle simple shortcodes, ZWJ sequences with one or multiple tones.
    *   `detone_emojis(text: str) -> str`: Remove skin-tone modifier codepoints from emoji in a string.
        *   Ensure plain emoji pass unchanged.
        *   Handle simple emoji with tone, ZWJ sequences with one or multiple tones.
        *   Work correctly within longer sentences.

*   Implement SQL generation utilities in `weave/trace_server/orm.py`:
    *   `ParamBuilder` class:
        *   `__init__(self, database_type: str, prefix: str = "") -> None`: Initialize with database type and optional prefix.
        *   `add(self, value, name: str, type: str = None) -> str`: Store parameter and return placeholder.
            *   Use `{name:type}` for ClickHouse, `:name` for SQLite.
            *   Default type to 'UInt64' for integers if omitted.
            *   Use prefix for auto-generated names.
        *   `get_params(self) -> dict`: Return dict of parameter name-value mappings.
    *   `Column` class:
        *   `__init__(self, name: str, type: str, nullable: bool = False, db_name: str = None) -> None`: Define a column with optional nullable flag and physical name.
    *   `Table` class:
        *   `__init__(self, name: str, columns: list) -> None`: Initialize with table name and columns list.
        *   `create_sql(self) -> str`: Return 'CREATE TABLE IF NOT EXISTS' SQL statement.
        *   `drop_sql(self) -> str`: Return 'DROP TABLE IF EXISTS' SQL statement.
        *   `select(self) -> SelectBuilder`: Return a new `SelectBuilder` for the table.
    *   `SelectBuilder` class:
        *   `limit(self, n: int) -> SelectBuilder`: Add a LIMIT clause.
        *   `fields(self, field_names: list) -> SelectBuilder`: Specify fields to include, supporting JSON paths.
        *   `prepare(self, database_type: str, param_builder: ParamBuilder = None) -> tuple`: Return SQL string, parameters dict, and fieldnames list.
            *   Use appropriate placeholders for LIMIT and JSON paths per database type.
    *   `_combine_conditions(conditions: list, operator: str) -> str`: Combine SQL conditions with 'AND'/'OR'.
        *   Raise `ValueError` for 'NOT' operator.
        *   Return empty string for empty list with valid operator.
        *   Return single condition unchanged if only one.
        *   Combine multiple conditions with correct bracketing.
    *   `_transform_external_field_to_internal_field(field: str, all_columns: list, json_columns: list, param_builder: ParamBuilder = None) -> tuple`: Translate external field name to SQL expression.
        *   Raise `ValueError` if field cannot be resolved.
        *   Return tuple with SQL expression and set of physical column names.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.