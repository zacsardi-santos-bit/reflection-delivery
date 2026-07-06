Remove the cached null count field from the Column struct and implement a method to compute the null count on demand from the null bitmap. Ensure all operations on the column continue to function correctly without maintaining a separate count. Add bulk pre-allocation methods for fixed-length numeric and decimal types.

*   Remove the `nullCount int` field from the Column struct in `util/chunk/column.go`.
*   Implement a `nullCount()` method in `util/chunk/column.go`:
    *   Compute and return the number of null entries by counting zero bits in the `nullBitmap`.
*   Implement a `SetNull(rowIdx int, isNull bool)` method in `util/chunk/column.go`:
    *   Clear the bit at `rowIdx` in `nullBitmap` if `isNull` is true (marking null).
    *   Set the bit if `isNull` is false (marking not-null).
    *   Ensure `nullCount()` reflects changes accurately.
*   Implement pre-allocation methods in `util/chunk/column.go`:
    *   `PreAllocInt64(length int)`: Reset the column to `length` null int64 entries.
    *   `PreAllocUint64(length int)`: Reset the column to `length` null uint64 entries.
    *   `PreAllocFloat32(length int)`: Reset the column to `length` null float32 entries.
    *   `PreAllocFloat64(length int)`: Reset the column to `length` null float64 entries.
    *   `PreAllocDecimal(length int)`: Reset the column to `length` null decimal entries.
    *   Each method must ensure the column's previous contents are replaced, not appended.
    *   After pre-allocation, `nullCount()` must return `length`, and all entries must be null.
*   Ensure that after `PreAllocXxx(n)` followed by `AppendXxx(v)`, the column has `n+1` entries:
    *   The entry at index `n` must not be null.
    *   Typed accessors (e.g., `Int64s()`) must return a slice of length `n+1` with the value `v` at index `n`.
*   Update all existing code in `util/chunk` that read or wrote the `nullCount` field:
    *   Replace with calls to `nullCount()` or compute null counts locally without storing them.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.