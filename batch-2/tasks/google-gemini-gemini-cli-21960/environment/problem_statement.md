## Description

There are several related bugs around retry status and cancellation in the streaming UI. When a user cancels an in-progress request, lingering retry events can still update the retry status display — causing a stale "retrying..." message to appear even after cancellation. Similarly, retry status phrases and cancel hints are shown in states where no active request is occurring (e.g., when the system is idle but a retry phrase happens to still be set). Finally, the underlying retry utility can fire retry notifications even when the operation has already been aborted, which contributes to these stale UI states.

## Expected Behavior

- The escape-to-cancel hint should only appear while a request is actively being processed (in the responding state). It should not appear when the system is idle, even if a loading phrase happens to be set.
- Retry status loading phrases should only be shown while the system is actively responding. When idle, no retry phrase should be produced even if retry status data is present.
- Retry status should be immediately cleared when a request is cancelled, and any retry events that arrive late (after cancellation) should be silently ignored.
- The retry utility should check for abort signals before firing retry callbacks. If the signal is already aborted at the point where a retry notification would be sent, the operation should abort cleanly without invoking the retry callback or logging any warnings.

## Why This Matters

These bugs cause confusing UI states where "retrying..." indicators or cancel hints appear when there is nothing actually being retried or cancelled. Fixing them keeps the UI consistent with the actual state of the system and prevents stale notifications from confusing users.
