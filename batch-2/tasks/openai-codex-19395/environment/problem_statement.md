## Description

When a session is configured with an explicit permission profile alongside a specific older sandbox policy variant, submitting a user message discards the configured permission profile entirely. The submitted message then goes out without any permission profile attached, which bypasses the intended access control settings for that session.

## Expected Behavior

- When a session has a permission profile configured, that profile should be included in every user turn submission, regardless of which sandbox policy variant the session uses.
- In particular, sessions using the legacy "external sandbox" policy variant should still propagate the permission profile on submission, just like sessions using any other policy variant.

## Why This Matters

This bug causes a subtle mismatch: the session is configured with a permission profile, but user messages are submitted as if no profile exists. Any consumer of the submitted user turn that relies on the permission profile for authorization or sandbox enforcement will see a missing value instead of the configured one. The result is unpredictable or overly permissive behavior for sessions that use the older external sandbox policy alongside a managed permission profile.
