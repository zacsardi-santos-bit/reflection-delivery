## Description

The release dashboard footer currently shows action buttons without properly accounting for all release states. Specifically, when a release has been archived, the footer still attempts to display a primary action button — but archived releases have no meaningful publishing workflow, so no primary action should appear. Only the overflow menu should remain in the footer for archived releases.

## Expected Behavior

The footer of the release dashboard should display contextually appropriate action buttons depending on the current release state:

- For active releases ready to publish, a publish button should be visible.
- For releases in a scheduled or pre-scheduled state, a schedule or unschedule button should appear.
- For releases that have already been published, a revert option should be shown.
- For **archived** releases, no primary action button should appear — only the overflow menu button should remain in the footer actions area.

## Why This Matters

Showing irrelevant action buttons for archived releases is misleading to users. Since an archived release cannot be published, scheduled, or reverted, displaying publish-related actions in the footer creates unnecessary confusion. The footer should only present actions that are meaningful for the current release state.
