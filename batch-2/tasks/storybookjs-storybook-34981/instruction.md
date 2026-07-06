Implement a notification system in the core dev server to detect when a cached review becomes outdated due to source file changes. Ensure the review addon subscribes to these notifications, marking reviews as stale when necessary, and manage the staleness status across multiple browser tabs.

*   Implement `subscribeToSourceFileChanges` in `code/core/src/core-server/change-detection/source-changes.ts`:
    *   Accept a listener callback that receives a `FileChangeEvent`.
    *   Return an unsubscribe function to stop the listener from receiving further events.

*   Implement `notifySourceFileChange` in `code/core/src/core-server/change-detection/source-changes.ts`:
    *   Deliver `FileChangeEvent` to all registered listeners.
    *   Ensure errors in listeners do not propagate and all listeners are called.

*   Implement `internal_resetSourceFileChangeListeners` in `code/core/src/core-server/change-detection/source-changes.ts`:
    *   Remove all registered listeners to ensure no events are delivered after reset.

*   Re-export `subscribeToSourceFileChanges` and `internal_resetSourceFileChangeListeners` from `code/core/src/core-server/change-detection/index.ts`.

*   Update `experimental_serverChannel` in `code/addons/review/src/preset.ts`:
    *   Accept an optional `subscribeToSourceFileChanges` property in `serverOptions`.
    *   Allow custom source-file-change subscription implementations.

*   Ensure the review addon:
    *   Subscribes to source-file change notifications.
    *   Marks cached reviews as stale if changes occur after the grace window from the `createdAt` timestamp.
    *   Updates the cached review with `stale: true` and emits `EVENTS.REVIEW_STALE` on the channel.
    *   Ignores changes within the grace window, emitting no staleness or events.
    *   Emits `EVENTS.REVIEW_STALE` only once per review.
    *   Clears staleness when a new review is pushed via `PUSH_REVIEW`.
    *   Includes `stale: true` in `DISPLAY_REVIEW` payload if the review is stale when `REQUEST_REVIEW` is received.

*   Add `REVIEW_STALE` to the `EVENTS` object in `code/addons/review/src/constants.ts`.

*   Add an optional `stale` boolean field to the `ReviewState` interface in `code/addons/review/src/review-state.ts`.

*   Ensure `FileChangeEvent` is defined in `code/core/src/core-server/change-detection/adapters/index.ts` with `kind` and `path` fields.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.