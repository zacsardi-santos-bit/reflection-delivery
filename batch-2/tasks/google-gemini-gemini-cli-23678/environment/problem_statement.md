# Track Onboarding Duration in Telemetry

## Description

When a user successfully completes onboarding, the system records that the onboarding succeeded and the user's tier, but it does not capture how long the onboarding process actually took. This makes it impossible to monitor onboarding performance, identify regressions, or compare performance across different user tiers.

## Expected Behavior

- The onboarding success event should include the elapsed time of the onboarding process in milliseconds.
- This duration should be recorded across all telemetry sinks: structured event logging (Clearcut), observability logs (OTEL), and metrics.
- The metrics system should record the duration as a histogram measurement when the duration is available, in addition to the existing success counter.
- The observability log body should include the duration alongside the tier information.
- The observability log attributes should include the numeric duration value.
- When the duration is not provided, the histogram metric should not be recorded.
- The user setup flow should measure how long the onboarding takes and include that duration when reporting the onboarding success event.

## Why This Matters

Without duration tracking, it is impossible to analyze onboarding performance trends over time or across user tiers. Adding this measurement enables teams to monitor the onboarding experience and detect performance regressions early.
