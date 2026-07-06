## Description

The notification system tests have gaps that reduce their reliability. Some notification component tests are entirely disabled (skipped), and others bypass the actual notification registration and display pipeline by rendering components directly. This means regressions in notification visibility logic — such as when a notification should or should not appear based on user authentication state, module connection status, or dismissal history — are not caught by the tests.

## Expected Behavior

- A test utility should be available to register specific notifications into the test data store, making it straightforward to set up test scenarios that reflect how notifications are managed in production.
- The utility should support both an "overwrite" mode (only the provided notifications are registered) and a default mode (a baseline set of notifications is included alongside any extras).
- Notification tests for authentication errors and linked-accounts banners should render through the same notification display pipeline used in production, so conditions like authentication state, module connectivity, and dismissal state are all exercised correctly.
- The authentication error notification for a specific missing permission (related to tag detection) should only appear when that is the **only** missing permission. When multiple permissions are missing, the general authentication error notification should appear instead.
- Tests for the linked-accounts success notification should no longer be skipped, and should validate rendering and non-rendering across all relevant conditions (module inactive, accounts not linked, report has data, notification dismissed, wrong view context).

## Why This Matters

Tests that skip notification components or bypass the notification pipeline provide false confidence. The goal is for notification visibility conditions to be tested through the same code path that production users experience, so that any regression in notification logic is caught automatically.
