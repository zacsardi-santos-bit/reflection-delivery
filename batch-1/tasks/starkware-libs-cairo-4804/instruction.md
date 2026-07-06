Implement the necessary changes to correct the order of optimization passes in the Cairo compiler's lowering pipeline. Ensure that the redundant remapping elimination pass runs before the block reorganization pass, and update the behavior of the block reorganizer accordingly.

*   Update the `reorganize_blocks` function in `crates/cairo-lang-lowering/src/reorganize_blocks.rs`:
    *   Remove the internal guard that prevents merging blocks with non-empty incoming remappings.
    *   Ensure the function can unconditionally merge blocks reachable through a single unconditional jump.

*   Modify the lowering optimization pipeline in `crates/cairo-lang-lowering/src/db.rs`:
    *   Ensure `optimize_remappings` is called before `reorganize_blocks`.
    *   Reverse any previous order where `reorganize_blocks` was called before `optimize_remappings`.

*   Update snapshot test data:
    *   Modify the file `crates/cairo-lang-lowering/src/optimizations/test_data/option` to reflect the new, more compact IR.
    *   Ensure that trivial passthrough blocks are eliminated and block identifiers are renumbered sequentially.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.