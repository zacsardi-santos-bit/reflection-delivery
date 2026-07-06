## Description

The key-value store watch functionality has a significant limitation: it requires a key to already have a value before a watch can be successfully started. If you try to watch a key that has no value yet, the watcher silently fails or skips values instead of waiting for the first write. This forces callers to pre-populate keys with placeholder values before starting any watch — a fragile workaround that should not be necessary.

Additionally, the prefix-watch feature has two bugs:
1. It strips the prefix from key names before delivering them to the callback, so callers receive incomplete key names.
2. It can deliver duplicate notifications for keys that have not actually changed, rather than reporting each changed key exactly once.

## Expected Behavior

- Watching a key that does not yet have a value should work correctly. The watcher should block and wait for the first value to be written, then report it — no pre-population required.
- Watching a key prefix should deliver the full key name (including the prefix) to the callback.
- Watching a key prefix should report each changed key exactly once, not produce duplicate notifications for unchanged keys.
- A reusable string codec implementation should be available in the shared codec package rather than being duplicated in individual test or client files.

## Why This Matters

These limitations make it impossible to reliably use the watch APIs in real scenarios where key creation and observation happen concurrently. Consumers of the watch API should not need to know whether a key already exists; they should simply subscribe and receive updates as they happen.
