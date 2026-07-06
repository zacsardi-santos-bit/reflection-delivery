Update the Cairo compiler to ensure that dictionary operation code is formatted correctly in the generated assembly output. Correct the indentation and spacing of embedded Python hint blocks to match the expected canonical format.

*   Modify the `fmt_hint_by_index` function in `crates/casm/src/hints/dict_squash.rs`:
    *   Ensure multi-line hint blocks use 4-level indentation (16 spaces).
    *   Remove trailing spaces from individual lines within hint blocks.
    *   Format single-line hints with exactly one space before and after the content within the hint delimiters.
*   Update the `Display` implementation for `Hint` in `crates/casm/src/hints/mod.rs`:
    *   Ensure the hint open delimiter is directly adjacent to the hint body without a trailing space.
    *   Ensure the hint close delimiter appears directly after the hint body without a leading space.
    *   Format single-line hints (e.g., AllocSegment, TestLessThan, etc.) with exactly one space of padding on each side within the delimiters.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.