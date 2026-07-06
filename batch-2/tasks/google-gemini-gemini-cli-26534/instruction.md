Implement the necessary changes to the context management pipeline for a CLI AI assistant to address issues with preview nodes, legacy session-context headers, and synchronization of the working buffer with the authoritative history.

* Update the `render` function in `packages/core/src/context/graph/render.ts`:
    * Accept a `previewNodeIds` parameter as the eighth positional argument of type `Set<string>`.
    * Filter out any node whose `id` is present in `previewNodeIds` before mapping nodes to history parts.
    * Ensure the `history` property in the return value contains only parts for non-preview nodes.

* Modify the `ContextGraphBuilder.processHistory` method in `packages/core/src/context/graph/toGraph.ts`:
    * Skip any history entry whose text content begins with `<session_context>`, regardless of its position in the history array.
    * Return an array of `BaseConcreteNode` objects corresponding to non-filtered entries, maintaining their original order.

* Enhance the `ContextWorkingBufferImpl.syncPristineHistory` method in `packages/core/src/context/pipeline/contextWorkingBuffer.ts`:
    * Accept a new authoritative list of pristine `ConcreteNode` objects.
    * Append any newly discovered pristine nodes to the end of the buffer.
    * Remove any working (non-pristine) node whose single pristine root was dropped from the new list.
    * Weave existing mutated or summarized working nodes into their correct chronological positions, matching the position of their original pristine source in the authoritative order.
    * Drop any non-pristine node with multiple pristine roots if any of those roots are absent from the new authoritative list.
    * Return a new `ContextWorkingBufferImpl` instance reflecting the synchronized state.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.