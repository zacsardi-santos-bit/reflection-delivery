## Description

The runtime watcher worker, which monitors game room instance events in real time, does not handle the case where the underlying event stream is closed. When the event source shuts down or disconnects, the worker blocks indefinitely instead of exiting gracefully. This prevents clean shutdown and makes recovery from a dropped runtime connection impossible.

## Expected Behavior

- When the event stream from the runtime watcher is closed, all internal event processors should stop and the worker should exit cleanly without returning an error.
- The runtime watcher connection should be properly cleaned up (stopped) when the event stream closes.
- The worker should still return an error if it cannot even begin watching (i.e., when the initial watch setup fails).
- Instance add, update, and delete events should continue to be processed correctly; failures during processing should be logged but should not stop the worker.

## Why This Matters

Without this fix, a closed event channel causes the worker to hang forever, which blocks any attempt to restart or recover the watcher. Proper lifecycle management requires the worker to detect a closed event stream and exit gracefully so that the surrounding system can respond appropriately.
