Implement a mechanism to allow the parser to respect cancellation signals during the tree-balancing phase. Ensure that the parser can resume from the balancing phase if cancelled, without re-parsing the entire input.

*   Modify the parser to stop and return `None` when a progress callback returns `true` during the tree-balancing phase.
    *   Ensure the parser does not produce a completed tree when cancelled during balancing.
*   Maintain a stable byte offset during the tree-balancing phase.
    *   Ensure the byte offset reported to the progress callback remains unchanged from the end of the main parse phase.
*   Allow the parser to resume from the balancing phase after a cancellation.
    *   Ensure `parse_with_options` resumes from the balancing phase without re-parsing the document when called again after a cancellation.
    *   Preserve `parse_options` and `parse_state` across calls to allow resumption.
*   Ensure a resumed parse completes successfully.
    *   Verify that the resumed parse returns `Some(Tree)` with `root_node().has_error() == false`.
    *   Confirm the parse tree has the correct number of top-level child nodes matching the original input.
*   Update the C implementation layer:
    *   Extend the timeout and progress-callback cancellation logic to run periodically during the tree-balancing phase in `lib/src/parser.c`.
    *   Add a flag to the parser struct to track interruptions during balancing.
    *   Make the internal tree-compression helper function accessible from `parser.c` by modifying `lib/src/subtree.c` and `lib/src/subtree.h`.
    *   Replace the old `ts_subtree_balance` function with a new version that checks the progress/timeout condition on each iteration.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.