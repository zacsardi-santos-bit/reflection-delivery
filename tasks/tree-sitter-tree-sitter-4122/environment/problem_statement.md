## Description

When parsing large inputs, the parser performs a final tree-balancing step after all tokens have been processed. For very large inputs (such as files with many repeated structures), this balancing step can take a significant amount of time. However, the cancellation mechanism — which allows callers to interrupt parsing via a timeout or a progress callback — does not apply during this balancing phase. Once the parser enters balancing, it cannot be stopped until it finishes, even if the caller has signaled a timeout or cancellation.

## Expected Behavior

- When a progress callback signals cancellation during tree balancing, the parser should stop and return no result (indicating the parse was not completed).
- The progress callback should be invoked periodically during tree balancing, with the byte offset remaining stable (not advancing) during this phase — allowing callers to detect that balancing is in progress.
- If parsing is cancelled during the balancing phase, the caller should be able to resume parsing without starting over. A subsequent parse call (without resetting the parser) should pick up from the balancing step where it left off, rather than re-processing the entire input.
- A resumed parse following a balancing cancellation should complete successfully and produce a correct parse tree.

## Why This Matters

For applications that need to enforce time limits on parsing (e.g., language servers that must stay responsive), it is important that cancellation works at all stages of parsing — not just during token processing. Without this fix, a parse job that enters the balancing phase becomes uninterruptible, which can cause unacceptable latency spikes on large inputs.
