Implement enhancements to the graph framework to improve handling of nested subgraphs and interrupts. Ensure that subgraph states are easily accessible, updates are straightforward, and streaming behavior is consistent across interrupt boundaries.

*   Update the `PregelTask` class:
    *   Ensure each `PregelTask` in the `StateSnapshot` has a `state` field containing a configurable dictionary with `thread_id` and `checkpoint_ns` keys for subgraph checkpoints.
    *   Remove reliance on a separate `subgraphs` dictionary in `StateSnapshot`.

*   Modify the `get_state` method:
    *   Accept a task's `state` configurable dictionary as the `config` argument to return the inner `StateSnapshot` of the subgraph.
    *   When `subgraphs=True`, ensure each `PregelTask`'s `state` field is a full nested `StateSnapshot`.

*   Adjust the `update_state` method:
    *   Allow a task's `state` configurable dictionary as the `config` argument to update the subgraph's state.
    *   Ensure subsequent calls to `get_state` reflect these updates.

*   Enhance streaming behavior in the `stream` method:
    *   For `stream_mode="values"` and resuming from an interrupt, emit the current graph state as the first output item.
    *   For `stream_mode="updates"`, include the last-checkpointed node output at the start of the resumed stream.

*   Update the `ainvoke` method:
    *   Ensure it returns the final state dictionary when invoked with `None` input to resume after an interrupt.

*   Implement forking from historical checkpoints:
    *   Allow `update_state` with `None` values on a historical checkpoint config to create a fork.
    *   Ensure streaming from the resulting config with `subgraphs=True` replays execution from that point.

*   Modify the `get_graph` method:
    *   With `xray=1`, render subgraph nodes using namespaced names and annotate interrupt nodes with their settings.
    *   Ensure the `draw_mermaid()` method produces a correct Mermaid diagram string.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.