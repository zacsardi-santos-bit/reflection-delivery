## Description

The context pipeline that manages conversation history for this CLI tool has several gaps that need to be addressed. Preview nodes — temporary, in-flight entries that represent pending or speculative state — are currently being included in the final conversation history sent to the AI model, which pollutes the model's context with content that was never meant to be permanent. Additionally, legacy session-context header blocks, which may appear at any position in a session's history, are not filtered out and instead appear as real user messages, causing unexpected behavior.

Finally, the working buffer that tracks processed conversation nodes (such as masked entries or rolling summaries) has no mechanism to synchronize with the upstream authoritative history. When new messages are added or old ones are removed from the authoritative source, the working buffer cannot reconcile those changes: it cannot append newly seen nodes, it cannot drop processed nodes whose original source was removed, and it cannot maintain the correct chronological order when both mutations and new additions occur simultaneously.

## Expected Behavior

- Preview nodes must be excluded from the rendered conversation history.
- Legacy session-context header blocks must be silently skipped regardless of where they appear in the history.
- The working buffer must expose a synchronization method that, given the new authoritative node list:
  - Appends any nodes that are newly present.
  - Removes any processed nodes whose original source node has been dropped.
  - Correctly positions retained processed nodes (mutated or summarized) relative to new additions.
  - Drops any multi-root processed node if even one of its roots has been removed.

## Why This Matters

Without these fixes, the context window sent to the model can contain stale preview state, legacy headers, and desynchronized processed entries, leading to incorrect or confusing model responses and potential token waste.
