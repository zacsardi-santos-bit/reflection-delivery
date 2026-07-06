## Description

The lecture editing page has no unsaved-changes guard. If a user makes changes to a lecture's title, description, channel name, or time period and then navigates away — either by clicking a link or using the browser's back button — those changes are silently discarded without any warning. This is a poor user experience, especially for instructors who may spend several minutes configuring a lecture.

## Expected Behavior

- When a user has made changes to the lecture's title section (title, description, channel name) or period section (visible date, start date, end date) and attempts to leave the edit page, a confirmation dialog should appear.
- The dialog should give the user the choice to either discard their changes and continue navigating away, or to return to the edit page and keep editing.
- If the user has not made any changes, or if the warning mechanism is not active, navigation should proceed without showing any dialog.
- The lecture's date range settings (visible date, start date, end date) should be managed by a standalone, reusable component that can be shared between the regular edit view and the wizard mode.
- The lecture edit component should track the state of the lecture at the time of page load so it can accurately determine whether any changes have been made.

## Why This Matters

Without this guard, instructors risk accidentally losing their work when navigating away from the lecture edit form. Adding an unsaved-changes warning makes the editing experience more forgiving and prevents silent data loss. Extracting the date period settings into a shared component also improves maintainability and prepares the codebase for a more reactive, signal-based architecture.
