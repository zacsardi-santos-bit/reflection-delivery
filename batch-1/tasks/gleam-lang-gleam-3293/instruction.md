Implement support for endianness and signedness in JavaScript code generation for Gleam bit arrays. Update the runtime functions and compiler to handle little-endian and big-endian byte ordering, signed and unsigned integer interpretation, and 32-bit float support consistently across all targets.

*   Update JavaScript runtime functions in `compiler-core/templates/prelude.mjs`:
    *   Implement `sizedInt(value: number, size: number, isBigEndian: boolean): Uint8Array` to convert integers to byte arrays with specified endianness.
    *   Implement `sizedFloat(float: number, size: number, isBigEndian: boolean): Uint8Array` to convert floats to byte arrays with specified endianness and size.
    *   Implement `byteArrayToInt(byteArray: Uint8Array, start: number, end: number, isBigEndian: boolean, isSigned: boolean): number` to read integers from byte arrays with specified endianness and signedness.
    *   Implement `byteArrayToFloat(byteArray: Uint8Array, start: number, end: number, isBigEndian: boolean): number` to read floats from byte arrays with specified endianness.
*   Update the `BitArray` class methods:
    *   Update `intFromSlice(start: number, end: number, isBigEndian: boolean, isSigned: boolean): number` to read integers with specified parameters.
    *   Implement `floatFromSlice(start: number, end: number, isBigEndian: boolean): number` to read floats with specified parameters.
*   Modify the Gleam-to-JavaScript compiler in `compiler-core/src/javascript/expression.rs` and `compiler-core/src/javascript/pattern.rs`:
    *   For integer bit array construction, emit `sizedInt(value, size, true)` for big-endian and `sizedInt(value, size, false)` for little-endian.
    *   For float bit array construction, emit `sizedFloat(value, size, true)` for big-endian and `sizedFloat(value, size, false)` for little-endian.
    *   For integer bit array pattern matching, emit `.intFromSlice(start, end, isBigEndian, isSigned)` or `.byteAt(offset)` for 1-byte unsigned.
    *   For float bit array pattern matching, emit `.floatFromSlice(start, end, isBigEndian)`.
    *   Replace the import of `float64Bits` with `sizedFloat`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.