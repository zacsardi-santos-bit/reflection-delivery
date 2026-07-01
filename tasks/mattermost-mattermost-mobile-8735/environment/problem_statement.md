## Description

We need to add support for a configurable channel banner feature in the mobile app. Admins can configure a banner with custom text and a background color for channels. The banner should appear at the top of a channel header and be tappable to reveal more details in a bottom sheet.

## Expected Behavior

- The banner should only be visible in standard and private channels — not in direct messages or group message channels.
- The banner should only appear for users on a premium license; professional and enterprise license tiers should not see banners.
- The banner must have all required fields populated (enabled flag set to true, non-empty text, and a non-empty background color) before it renders.
- When the user taps the banner, a bottom sheet should slide up with the title "Channel Banner" and include a close button.
- Text on the banner should be automatically colored to remain legible against the banner's background — the app should pick either black or white text depending on whether the background is light or dark.
- The contrast selection utility must handle invalid or malformed color inputs gracefully by returning an empty string rather than crashing.

## Why This Matters

Without this feature, admins have no way to surface important channel-level notices directly in the channel header on mobile. The banner provides a persistent, visible way to communicate context-specific information to channel members.

The contrast utility is important to ensure accessibility — without it, banner text may be unreadable on certain background colors.
