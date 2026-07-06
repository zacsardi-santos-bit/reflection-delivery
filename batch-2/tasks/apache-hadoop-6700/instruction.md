Implement a unified range-validation function for Hadoop's vectored read API to ensure consistent error handling across different filesystem implementations. Validate and sort file ranges, check for overlaps, out-of-bounds conditions, and handle null inputs with clear exceptions. Update supporting utilities and configurations as specified.

*   Implement `validateAndSortRanges` in `VectoredReadUtils`:
    *   Validate input list for null, empty, negative offsets, negative lengths, and overlapping/duplicate ranges.
    *   Sort ranges by offset and return the sorted list.
    *   Throw `NullPointerException` for null input, `IllegalArgumentException` for empty list or overlapping/duplicate ranges, and `EOFException` for negative offsets or out-of-bounds conditions when file length is provided.
*   Implement `sortRanges` in `VectoredReadUtils` to return a sorted `List<? extends FileRange>`.
*   Update `validateVectoredReadResult` in `ContractTestUtils` to accept `List<FileRange>`, `byte[] originalData`, and `long baseOffset`.
*   Add utility methods in `ContractTestUtils`:
    *   `range(long offset, int length)` to create a single `FileRange` list.
    *   `range(List<FileRange> fileRanges, long offset, int length)` to add a `FileRange` to an existing list.
    *   `totalReadSize(List<FileRange> fileRanges)` to calculate total byte count.
*   Add `VECTOR_IO_EARLY_EOF_CHECK` constant to `ContractOptions`.
*   Update `localfs.xml` to include `fs.contract.vector-io-early-eof-check` set to `true`.
*   Ensure vectored read operations throw `IllegalArgumentException` for overlapping or duplicate ranges and `NullPointerException` for null lists or elements.
*   Relocate `TestVectoredReadUtils` to `org.apache.hadoop.fs.impl` package.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.