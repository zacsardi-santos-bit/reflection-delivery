Implement support for while loops in the Cairo compiler's lowering phase to allow them to compile end-to-end without errors. Ensure variable usage tracking and reporting correctly handle while loops, distinguishing them from other constructs.

*   Update the lowering pass to support while loop expressions:
    *   Ensure while loops lower to flat IR without producing diagnostic errors.
    *   Implement logic to evaluate the condition expression and perform a boolean branch:
        *   If the condition is true, execute the loop body and recursively call the generated loop function.
        *   If the condition is false, exit with an empty tuple result.

*   Enhance variable usage tracking:
    *   Record a usage entry for each while expression, keyed by the while expression's ID.
        *   Aggregate variable reads, writes, and introductions from both the condition expression and the body block.
    *   Record a usage entry for each loop expression, keyed by the loop expression's ID.
        *   Ensure the entry carries the same variable reads, writes, and introductions as the loop's body block.
    *   Ensure every expression ID key in the variable usage map corresponds to a plain block, a loop, or a while expression.

*   Update usage reporting:
    *   Prefix each entry with the expression kind label — 'Block', 'Loop', or 'While' — followed by the source file position (line and column).
    *   Ensure usage data is clonable to allow a loop expression's usage entry to be initialized by copying the body block's usage entry.

*   Create a test data file at `crates/cairo-lang-lowering/src/lower/test_data/while`:
    *   Use the `test_generated_function` runner format.
    *   Include at least one while loop test case with its expected lowering output.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.