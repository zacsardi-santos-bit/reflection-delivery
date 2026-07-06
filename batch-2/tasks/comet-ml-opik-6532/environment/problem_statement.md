## Description

The SDK's evaluation pipeline currently treats all evaluation outputs the same way — everything is submitted as a generic numeric feedback score. However, automated test suites produce a distinct type of result: pass/fail assertions (e.g. "must mention Paris", "must be polite") that are conceptually different from floating-point quality metrics. There is no dedicated path to record these binary assertion results separately from continuous scores.

## Expected Behavior

- A dedicated method should be available on the main client to submit assertion results as a batch. Each entry should carry at least a trace identifier, an assertion name, and a pass/fail status. Optional fields for failure reason and a per-item project name override should also be supported.
- The evaluation pipeline should automatically route results tagged as suite assertions to this new method, while routing all other numeric scores through the existing feedback-scores path. Items that failed during scoring should be excluded from both paths.
- The new assertion-results path should target a dedicated backend endpoint. If that endpoint is unavailable on an older self-hosted backend (indicated by a 404 or 405 response), the system should transparently fall back to the legacy feedback-scores mechanism. Once the fallback is triggered for a session, the system should remember this and skip the new endpoint for all subsequent calls, avoiding repeated failed attempts.
- Any API errors that are not endpoint-availability issues (e.g. server errors) should be surfaced to the caller rather than silently swallowed.

## Why This Matters

This allows test suite assertion results to be stored in a first-class way, making them queryable and actionable independently of generic numeric scores, while maintaining full backward compatibility with older backend deployments.
