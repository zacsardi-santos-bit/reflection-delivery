# Add Offline Detection and Notification

## Description

Currently, Site Kit has no way to detect or communicate to users when their internet connection is unavailable. If a user loses connectivity while using the plugin dashboard, they receive no feedback — features silently fail and there is no indication of why. We need a mechanism to actively monitor the user's connection status and surface a clear notification when they are offline.

## Expected Behavior

- The application should continuously monitor the user's internet connection, checking more frequently when offline and less frequently when online.
- When a user goes offline, a notification should appear informing them that they are currently offline and that some features may not be available.
- When the connection is restored, the notification should automatically disappear — no manual dismissal should be required for this transition.
- The connection state should be stored in the shared application data store so that all parts of the UI can react to it.
- An existing connection-state counter selector in the shared store has an incorrect name and must be corrected.

## Why This Matters

Users who lose their connection mid-session currently have no way of knowing whether a blank or broken dashboard is caused by a connectivity issue. A proactive offline notification prevents confusion and lets users know they should check their network before assuming something is wrong with the plugin itself.
