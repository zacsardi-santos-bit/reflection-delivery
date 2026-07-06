## Description

We need to add support for AI-generated transcripts in Pocket Casts, gating access to them behind a subscription. Currently, the transcript system treats all transcripts equally — there is no way to mark a transcript as AI-generated or to show a paywall when a free user tries to view one.

## Expected Behavior

- Each transcript should carry a flag indicating whether it was AI-generated or human-authored.
- When a free (unsubscribed) user opens a generated transcript that has actual content, the UI should display a paywall instead of the transcript text. Paid subscribers (Plus or Patron) should see the transcript normally.
- If the generated transcript has no content at all, the paywall should not be shown regardless of subscription status.
- When selecting among multiple available transcripts for an episode, the system should prefer human-authored transcripts over generated ones. If only generated transcripts exist, those should still be used.
- The episode data fetcher should support an additional Pocket Casts-specific transcript source. Transcripts from this source are automatically treated as AI-generated. Entries missing either a URL or a type should be ignored.
- When an episode has no transcripts from any source, the transcript update process should still run (with an empty list) rather than skipping entirely.
- A new database migration must be added to persist the generated transcript flag in local storage.

## Why This Matters

Pocket Casts produces AI-generated transcripts as a premium feature. Without this change, those transcripts would be visible to all users with no subscription gate. This update ensures that generated transcripts are properly attributed, selectively gated for free users, and preferred less than human-provided transcripts when both are available.
