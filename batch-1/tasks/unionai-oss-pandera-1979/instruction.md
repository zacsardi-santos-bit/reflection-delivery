Implement integration of pandera's Polars backend with pydantic for data validation and format conversion. Ensure that typed Polars DataFrames and schema objects can be used as fields in pydantic models, and support reading from and writing to various data formats.

*   Update `pandera/typing/polars.py` to include:
    *   A `DataFrame` class with:
        *   `from_format(obj: Any, config) -> pl.DataFrame`: Converts serialized data to a Polars DataFrame.
            *   Handle `config.from_format` values: `None`, callable, `"dict"`, `"csv"`, `"json"`, `"parquet"`, `"feather"`, `"pickle"`, `"json_normalize"`, and unrecognized formats.
            *   Raise `ValueError` with specific messages on failures or unsupported formats.
        *   `to_format(data: pl.DataFrame, config) -> Any`: Converts a Polars DataFrame to a specified format.
            *   Handle `config.to_format` values: `None`, callable, `"dict"`, `"csv"`, `"json"`, `"parquet"`, `"feather"`, `"pickle"`, `"json_normalize"`, and unrecognized formats.
            *   Raise `ValueError` with specific messages on failures or unsupported formats.
        *   `_get_schema_model(field) -> Any`: Extracts schema model from a pydantic v1 field object.
            *   Raise `TypeError` if `field.sub_fields` is empty/falsy.
        *   `pydantic_validate(obj: Any, schema_model) -> pl.DataFrame`: Validates `obj` against `schema_model`.
            *   Convert `SchemaInitError` to `ValueError`.
        *   `_pydantic_validate(obj: Any, field) -> pl.DataFrame`: Legacy pydantic v1 validator.
        *   `__get_pydantic_core_schema__(cls, _source_type: Any, _handler: GetCoreSchemaHandler) -> core_schema.CoreSchema`: Generate pydantic v2 core schema if `PYDANTIC_V2` is `True`.
    *   A `Series` class with a non-empty string docstring.
    *   A `polars_version()` function returning the installed Polars version as a `packaging.version.Version` object.
    *   Module-level attributes:
        *   `POLARS_INSTALLED`: Boolean indicating if Polars is importable.
        *   `T`: A `TypeVar` for internal use.
*   Ensure `DataFrame[Schema]` and `DataFrameModel` subclasses are usable as pydantic model field types.
    *   Handle valid and invalid data inputs, raising `ValidationError` or `TypeError` as appropriate.
    *   Support inheritance and optional columns in schema models.
    *   Automatically convert dicts and pandas DataFrames to Polars DataFrames.
*   Export necessary symbols and ensure compatibility with both pydantic v1 and v2.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.