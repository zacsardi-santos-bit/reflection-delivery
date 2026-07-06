I've been investigating security issues in ClickHouse's Arrow IPC format reader and found that it's vulnerable to heap out-of-bounds reads when given specially crafted input files.

*   When an Arrow IPC file's RecordBatch or FieldNode declares a row count larger than the actual data buffer can hold (for fixed-width types such as numeric, duration, fixed-size binary, date, dictionary index, and IPv4 columns), the query must be rejected with an INCORRECT_DATA error.

*   When per-element accessor arrays declare more rows than their data buffer supports (Boolean, Date32, Date64, Timestamp, Time32, Time64, Float16, Decimal128, and UUID columns), the query must be rejected with INCORRECT_DATA.

*   When a view-struct buffer (StringView, BinaryView) is too small to cover the declared number of rows, or when a view struct's internal length field claims more bytes than the variadic data buffer actually contains, or when a view struct carries a negative size field, the query must be rejected with INCORRECT_DATA.

*   When an offsets buffer is too small for the declared row count (String, Binary, JSON, BigNum/Int128, Geo/Point, IPv6 columns), including when a negative FieldNode length causes the offsets-buffer size check to pass trivially, the query must be rejected with INCORRECT_DATA.

*   When a data buffer is too small to satisfy a row's value_offset + row_size (BigNum/Int128, IPv6 columns), the query must be rejected with INCORRECT_DATA.

*   When buffer size arithmetic overflows a 64-bit integer (e.g., element size multiplied by a near-2^62 row count wraps to zero), the query must be rejected with INCORRECT_DATA rather than a memory allocation error.

*   When the validity bitmap (null/presence bitmap) is too small for the declared row count — including bitmaps on nested columns (list children, struct fields) that have been sliced or flattened — the query must be rejected with INCORRECT_DATA.

*   When a FieldNode.null_count field is set to the unknown sentinel (-1) over a large declared length but the associated validity bitmap is too small to cover that length, the query must be rejected with INCORRECT_DATA before any bitmap scan.

*   When List or LargeList column offsets are non-monotonic, contain a decreasing pair, or point past the end of the child values array, the query must be rejected with INCORRECT_DATA.

*   When a nested array's child FieldNode.length is inconsistent with the parent (zero-length child with non-zero parent offsets, FixedSizeList child shorter than length×stride, or a negative child FieldNode.length), the query must be rejected with INCORRECT_DATA.

*   When a Struct field's declared length is shorter than the parent struct's length, or when a sliced struct's child field is too short for the slice range (e.g., a 0-length field sliced at offset 1), or when a Map's value field is shorter than the keys field, the query must be rejected with INCORRECT_DATA.

*   When top-level RecordBatch or FieldNode lengths are negative or excessively large (including Int32, Bool, Decimal columns, empty Struct columns, and LargeList with a huge flattened child length derived from 64-bit offsets), the query must be rejected with INCORRECT_DATA.

*   Buffer and metadata validation must occur before any column memory reservation: a malformed declared length must produce INCORRECT_DATA rather than a memory allocation failure (CANNOT_ALLOCATE_MEMORY), even when that length would cause reserve() to request a huge allocation.

*   Validity bitmap validation must occur before building the Arrow table from record batches, so that Arrow's internal null-count computation (which scans the bitmap over the declared length) cannot read out of bounds on a truncated bitmap.

*   All the above validations must apply consistently across every affected data type reader and every nested column path (list, large list, fixed-size list, map, struct, dict-encoded columns).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.