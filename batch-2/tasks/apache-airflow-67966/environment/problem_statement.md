## Description

The CI pre-commit hook that validates breeze command configuration runs breeze from a cached installation that doesn't always reflect uncommitted local changes to the breeze source code. When the cache is stale, the hook computes command hashes and option groups from the old code — which means it either fails to trigger a needed regeneration, or worse, reverts a correctly-regenerated image back to the stale cached version.

## Expected Behavior

There should be a utility function in the CI pre-commit hook module that:

- Returns a complete copy of the current process environment with the local breeze source directory inserted at the front of the Python import path
- When the Python import path variable is unset, it should be set to just the local source directory path
- When the Python import path variable already has a value, the local source directory path should be prepended (with the appropriate path separator) so it takes priority over anything in the existing path
- The function must not modify the actual running process environment — it should only return a modified copy
- All other environment variables from the current process must be preserved in the returned copy

## Why This Matters

Developers editing the breeze sources locally should be able to rely on the pre-commit hook to evaluate their changes against the *current* code, not a stale build artifact. Without this fix, the hook silently uses old code and produces incorrect results, making the developer experience unreliable.
