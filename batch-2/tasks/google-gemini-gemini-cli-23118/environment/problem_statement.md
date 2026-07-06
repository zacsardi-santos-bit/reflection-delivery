## Description

The CLI currently has no telemetry instrumentation for the user authentication and onboarding flow. When a user authenticates for the first time, no events are recorded to track whether the process started or completed successfully. This makes it impossible to monitor onboarding success rates, diagnose drop-offs, or understand the distribution of user tiers among newly onboarded users.

## Expected Behavior

- When a user begins the authentication and onboarding process, a telemetry event should be recorded indicating that onboarding has started.
- When the onboarding flow completes successfully, a second telemetry event should be recorded that includes the user's tier information.
- Both events should be sent to the structured clearcut logging backend and the OpenTelemetry metrics pipeline.
- The user profile data returned after setup should include a flag indicating whether the user had previously completed onboarding, so the application can distinguish first-time users from returning ones.
- The component responsible for setting up users should accept the full application configuration as a parameter (rather than a narrowly-scoped callback), enabling it to access telemetry and session information needed to emit these events.

## Why This Matters

Without onboarding telemetry, it is difficult to understand how many users successfully complete authentication, what tier they end up on, and whether any patterns exist in users who fail to complete the process. Tracking onboarding start and success events is a foundational requirement for monitoring the health of the onboarding funnel.
