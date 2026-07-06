## Description

The post-test lifecycle hook in the extension system currently cannot attach custom scores or metadata to evaluation results. When a hook runs after each test case, it can observe the result, but any values it returns are silently discarded — they never make it into the persisted evaluation data or summary metrics.

This is a significant gap for advanced use cases. Developers who want to track things like session URLs, tool invocation counts, turn counts, cost estimates, or other computed metrics during a hook cannot currently surface those values in the evaluation results. The data simply disappears.

## Expected Behavior

- A post-test hook should be able to return custom named numeric scores and arbitrary metadata key-value pairs, which are then merged into the persisted evaluation result.
- Custom named scores returned by hooks should also appear in the per-prompt aggregate metrics in the evaluation summary.
- Hook-returned response metadata should be merged into the result's response metadata.
- Hooks must not be able to alter core result fields like whether the test passed, the main score, or the raw response output.
- When multiple hooks are configured, each hook in the chain should receive the already-enriched context from all prior hooks — not just the original pre-hook state.
- When a hook throws an error, the evaluation result for that test should still be saved; the error should not cause the row to be lost.

## Why This Matters

Without this capability, developers cannot use the hook system to enrich evaluation results with computed or externally-sourced metadata. The only workaround today is to modify providers or graders, which couples concerns that should be separate. Fixing this makes the extension hook system genuinely useful for attaching post-hoc signals to evaluation results.
