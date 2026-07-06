Implement automated tests for the accessibility consumer library's filtering logic to ensure nodes in an accessibility tree are correctly exposed to assistive technologies. Verify the behavior of the filtering functions under various scenarios.

Requirements:

*   Implement the `common_filter` function in `consumer/src/filters.rs`:
    *   Return `FilterResult::Include` for a normal node (e.g., a button).
    *   Return `FilterResult::ExcludeSubtree` for a node marked as hidden.
    *   Return `FilterResult::Include` for a hidden node that is focused.
    *   Return `FilterResult::ExcludeNode` for a `GenericContainer` node, allowing its children to be traversed.
    *   Return `FilterResult::ExcludeSubtree` for both a hidden parent node and all its child nodes.
    *   Return `FilterResult::Include` for a focused child node, even if its parent is hidden.
    *   Return `FilterResult::ExcludeNode` for a `TextRun` node, excluding the node itself but not affecting its subtree.

*   Implement the `common_filter_with_root_exception` function in `consumer/src/filters.rs`:
    *   Return `FilterResult::Include` for a `GenericContainer` node when it acts as root, whereas `common_filter` would return `ExcludeNode`.

*   Ensure the `FilterResult` enum in `consumer/src/filters.rs` includes the variants: `Include`, `ExcludeNode`, and `ExcludeSubtree`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.