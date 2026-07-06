Implement a structured error reporting system for a data validation library that distinguishes between schema-level and data-level errors. Allow configuration to run only one category of validation and ensure error messages are informative and programmatically accessible.

*   Update `pandera/config.py`:
    *   Define an enum `ValidationDepth` with members `SCHEMA_AND_DATA`, `SCHEMA_ONLY`, and `DATA_ONLY`.
    *   Ensure `ValidationDepth` is importable as `from pandera.config import ValidationDepth`.
    *   Create a global configuration object `CONFIG` with a `validation_depth` attribute of type `ValidationDepth`.
    *   Allow `CONFIG.validation_depth` to be read and written at runtime.

*   Modify `pandera/errors.py`:
    *   Update the `SchemaErrors` class:
        *   Change the `message` attribute to a nested dictionary format: `{"SCHEMA": {error_type: [entry, ...]}, "DATA": {error_type: [entry, ...]}}`.
        *   Ensure top-level keys in `message` depend on `CONFIG.validation_depth`:
            *   Include only 'SCHEMA' key if `CONFIG.validation_depth` is `SCHEMA_ONLY`.
            *   Include only 'DATA' key if `CONFIG.validation_depth` is `DATA_ONLY`.
            *   Include both 'SCHEMA' and 'DATA' keys if `CONFIG.validation_depth` is `SCHEMA_AND_DATA`.
        *   Ensure each error entry in `message` is a dictionary with keys: "schema", "column", "check", and "error".
        *   Implement `error_counts` to return a dictionary with plain string keys, such as 'CHECK_ERROR'.
        *   Format element-wise check failure messages as: "Column '{column_name}' failed element-wise validator number {n}: {check_name} failure cases: {failing_value}".
        *   Include the schema name in the string representation of `SchemaErrors`.
        *   Include 'out-of-order' in messages when columns or indices are out of order in an ordered schema.
        *   Raise `errors.SchemaError` when a required column is missing.

*   Ensure error messages for known error types match specified formats:
    *   'COLUMN_NOT_IN_SCHEMA': "column '{col}' not in DataFrameSchema {schema_repr}"
    *   'SERIES_CONTAINS_NULLS': "non-nullable series '{col}' contains null values:{values}"
    *   'WRONG_DATATYPE': "expected series '{col}' to have type {expected}, got {actual}"
    *   'DATAFRAME_CHECK': "Column '{col}' failed element-wise validator number {n}: {check} failure cases: {value}"

*   Group errors in `SchemaErrors.message`:
    *   Under 'DATA', group errors by check category, ensuring one entry per category.
    *   Under 'SCHEMA', group errors by schema error type, ensuring one entry per type.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.