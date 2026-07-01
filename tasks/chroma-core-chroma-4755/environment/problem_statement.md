## Description

The shared test helper that creates a temporary local storage instance for unit tests has a silent lifetime bug: the temporary directory it creates is deleted immediately after the helper runs, before any test code can actually use it.

The root cause is that the helper function consumes the guard object responsible for keeping the temporary directory alive, converting it to a plain path and dropping the guard inside the function. This causes the operating system to clean up the directory right away. Any test relying on this helper is therefore running against a storage backend whose underlying directory no longer exists on disk.

## Expected Behavior

- The helper should return the temporary directory guard alongside the storage instance as a pair.
- After the helper returns, the temporary directory must still exist on disk and be accessible.
- Callers should control when the directory is cleaned up by holding or dropping the returned guard.

## Why This Matters

This bug silently breaks any test that uses this shared helper: the storage path disappears before the test runs, leading to unexpected failures or flaky behavior. Because many tests across multiple components share this helper, the fix needs to happen at the source — in the helper itself — and callers need to be updated to accept the guard alongside the storage instance.
