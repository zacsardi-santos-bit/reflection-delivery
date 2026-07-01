Implement the missing encoding primitives and correct deserialization issues in the Fory Rust serialization library to enable cross-language interoperability with Java and Python. Add string utility functions to check Latin-1 encodability and compute byte length, create a 36-bit integer codec, and ensure compatible-mode deserialization initializes missing fields correctly.

*   Add public functions to the `fory_core::meta` module:
    *   Implement `is_latin(s: &str) -> bool` in `rust/fory-core/src/meta/string_util.rs` to return `true` if all characters in `s` have Unicode code points ≤ 255, otherwise `false`.
    *   Implement `get_latin1_length(s: &str) -> i32` in `rust/fory-core/src/meta/string_util.rs` to return the number of characters if `s` is Latin-1 encodable, or `-1` if not.

*   Extend the `fory_core::buffer` module:
    *   Implement `var_uint36_small(&mut self, value: u64)` in `rust/fory-core/src/buffer.rs` for the `Writer` struct to encode a 36-bit unsigned integer using 1 to 5 bytes.
    *   Implement `var_uint36_small(&mut self) -> u64` in `rust/fory-core/src/buffer.rs` for the `Reader` struct to decode the 36-bit unsigned integer encoded by the `Writer`.

*   Ensure compatible-mode deserialization:
    *   Use `Default::default()` to initialize missing fields in types that derive the `Default` trait.
    *   Support `Vec<Option<String>>` as a field type, initializing absent elements to `String::default()`.

*   Update Java test utilities:
    *   Overload `TestUtils.executeCommand` in `java/fory-test-core/src/main/java/org/apache/fory/test/TestUtils.java` to accept a `File workDir` parameter, setting the working directory for the process.
    *   Ensure the existing 3-argument `executeCommand` delegates to the new 4-argument overload with `workDir` as null.
    *   Log all environment variables except `DATA_FILE` and the full command string before execution.

*   Rename the test function `nullable_collection` to `nullable_container` in the compatible-mode test suite.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.