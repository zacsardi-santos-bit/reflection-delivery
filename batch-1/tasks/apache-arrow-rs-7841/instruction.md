Implement support for two new compact decimal array types in Arrow-rs: a 32-bit decimal type for values with up to 9 significant digits and a 64-bit decimal type for values with up to 18 significant digits. Ensure these types are fully integrated with Parquet, including correct handling of row-group and data-page-level statistics.

*   Create a `Decimal32Array` type in the arrow-array crate:
    *   Construct from `Vec<i32>`.
    *   Implement `with_precision_and_scale(precision: u8, scale: i8) -> Result<Decimal32Array>`.
    *   Re-export via the arrow crate as `arrow_array::Decimal32Array`.
*   Create a `Decimal64Array` type in the arrow-array crate:
    *   Construct from `Vec<i64>`.
    *   Implement `with_precision_and_scale(precision: u8, scale: i8) -> Result<Decimal64Array>`.
    *   Re-export via the arrow crate as `arrow_array::Decimal64Array`.
*   Add new variants to the `DataType` enum in the arrow-schema crate:
    *   `DataType::Decimal32(u8, i8)` for `Decimal32Array` columns.
    *   `DataType::Decimal64(u8, i8)` for `Decimal64Array` columns.
*   Update the Parquet statistics reader:
    *   Handle `DataType::Decimal32(precision, scale)` and `DataType::Decimal64(precision, scale)`.
    *   For `Decimal32` with precision ≤ 9, decode INT32-stored statistics into `Decimal32Array`.
    *   For `Decimal64`, decode INT32-stored statistics for precision ≤ 9 and INT64-stored statistics for precision 10–18 into `Decimal64Array`.
    *   Ensure statistics are reported as exact.
*   Implement data-page-level statistics handling:
    *   Correctly process `DataType::Decimal32(8, 2)`, `DataType::Decimal64(8, 2)`, and `DataType::Decimal64(10, 2)`.
    *   Handle pages containing only null values.
*   Ensure null counts and row counts are correctly reported per row group for both `Decimal32Array` and `Decimal64Array` columns.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.