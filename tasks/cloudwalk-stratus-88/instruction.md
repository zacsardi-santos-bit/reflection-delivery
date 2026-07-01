Implement a conversion for the Index type to a signed 32-bit integer using Rust's From trait. Ensure that the conversion preserves the original value exactly, facilitating seamless interaction with PostgreSQL's 32-bit integer columns.

*   Implement the From trait for the Index type to convert it into an i32.
    *   Ensure the conversion is value-preserving, such that an Index created with a value of 42 converts to an i32 value of 42.
    *   Use the standard Rust From/Into trait pattern to facilitate idiomatic conversions.
*   Place the From<Index> for i32 implementation in the file `src/eth/primitives/index.rs`.
*   Ensure the function signature is `fn from(value: Index) -> i32`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.