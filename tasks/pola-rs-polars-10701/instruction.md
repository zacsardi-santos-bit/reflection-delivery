Improve the Polars implementation of the dataframe interchange protocol by addressing current limitations and ensuring proper data handling. Implement necessary functions to convert and import data types correctly, provide clear error messages, and support zero-copy imports where possible.

*   Implement `_column_to_series` to convert interchange columns to Polars Series:
    *   Handle sentinel null values for temporal types.
    *   Raise `TypeError` for invalid sentinel values with a specific message.

*   Implement `_string_column_to_series` to convert string columns:
    *   Raise `RuntimeError` if offsets buffer is missing.

*   Implement `_categorical_column_to_series` to convert categorical columns:
    *   Raise `NotImplementedError` for non-dictionary categoricals or non-string categories.

*   Implement `_construct_data_buffer` to create data Series from buffers:
    *   Handle logical/temporal dtypes and boolean buffers.
    *   Raise `CopyNotAllowedError` for byte-packed boolean buffers when `allow_copy=False`.

*   Implement `_construct_offsets_buffer` to create Int64 offsets Series:
    *   Raise `CopyNotAllowedError` for non-Int64 buffers when `allow_copy=False`.

*   Implement `_construct_validity_buffer` to handle null representations:
    *   Return `None` for NON_NULLABLE or null_count=0.
    *   Handle USE_BITMASK, USE_BYTEMASK, USE_NAN, USE_SENTINEL, and unknown null types appropriately.

*   Implement `_construct_validity_buffer_from_bitmask` for bitmask buffers:
    *   Handle null_value conditions and support sliced buffers.

*   Implement `_construct_validity_buffer_from_bytemask` for bytemask buffers:
    *   Always raise `CopyNotAllowedError` when `allow_copy=False`.

*   Implement `dtype_to_polars_dtype` to map interchange Dtype tuples to Polars types:
    *   Handle CATEGORICAL and unsupported types with specific error messages.

*   Implement `get_buffer_length_in_elements` to compute buffer element count:
    *   Raise `ValueError` for non-byte-multiples.

*   Implement `polars_dtype_to_data_buffer_dtype` to map logical to physical dtypes:
    *   Handle unsupported types with `NotImplementedError`.

*   Implement `polars_dtype_to_dtype` to map Polars dtypes to interchange Dtypes:
    *   Handle CATEGORICAL and unsupported types with specific error messages.

*   Update `PolarsColumn` class to handle global categorical Series:
    *   Ensure `_col` attribute matches the original series.
    *   Raise `CopyNotAllowedError` for `get_chunks(allow_copy=False)` on global categoricals.

*   Update `pl.from_dataframe` to handle various data conversions:
    *   Convert categorical columns to Polars Enum.
    *   Handle empty DataFrames, boolean columns, and NaN values appropriately.
    *   Raise specific errors for unsupported categorical keys and boolean columns.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.