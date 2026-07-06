## Description

The tensor comparison tool currently treats each forward-pass step independently, so comparing tensors across multiple steps produces one separate comparison result per step. This works for single-step comparisons, but for multi-step scenarios it clutters the output and makes it harder to see whether data aligns overall. A simpler mode is needed that just concatenates the tensors from all steps into a single tensor and compares that one combined result.

## Expected Behavior

- A new "concat steps" operating mode should be available. When enabled, tensors from multiple forward passes are concatenated in step order into one combined tensor before comparison, yielding a single comparison record per logical tensor name.
- Concatenation must happen along the token or sequence dimension. The correct dimension should be inferred automatically: a named token dimension takes priority, a named sequence dimension is the fallback, and dim 0 is used when no such dimension annotation exists.
- When the two sides have different total lengths after concatenation, both are truncated to the minimum length before comparison.
- The existing smart alignment mode (which uses auxiliary tensors to match tokens across sequences) must remain available and should be explicitly selectable.
- The token-aligner internals used by the smart alignment mode should be organized under a dedicated sub-package to cleanly separate them from the new concat-steps logic.
- A helper utility for loading per-step sequence lengths from dump files must be provided. It should support both recognized dump formats, return a mapping from step index to a list of per-sequence lengths, and return nothing when the required metadata or recognized framework cannot be found.

## Why This Matters

Users running multi-step model comparisons should be able to get a single, easy-to-interpret result instead of one entry per step, without having to manually merge data. The new concat mode makes this the default behavior, while the more sophisticated alignment mode is preserved for cross-framework or sequence-reordering use cases.
