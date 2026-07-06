Implement duration tracking for the onboarding success event in the telemetry system to monitor onboarding performance over time. Update the system to include this duration in structured event logging, observability logs, and metrics.

*   Update the `OnboardingSuccessEvent` class:
    *   Add a constructor with parameters `userTier: string` and `duration_ms: number`.
    *   Ensure the class exposes `userTier` and `duration_ms` properties.
    *   Export this class from `packages/core/src/telemetry/index.ts`.

*   Modify the `logOnboardingSuccessEvent` method in the Clearcut logger:
    *   Include onboarding duration in the event metadata using the `EventMetadataKey.GEMINI_CLI_ONBOARDING_DURATION_MS` key.
    *   Store the duration value as a string.

*   Add a new constant in the `EventMetadataKey` enum:
    *   Define `GEMINI_CLI_ONBOARDING_DURATION_MS` to store the onboarding duration in Clearcut event metadata.

*   Update the `logOnboardingSuccess` function:
    *   Format the OTEL log body as: 'Onboarding succeeded. Tier: {userTier}. Duration: {duration_ms}ms'.
    *   Include `duration_ms` as a numeric field in OTEL log attributes.
    *   Pass `duration_ms` as a third argument to `recordOnboardingSuccess`.

*   Enhance the `recordOnboardingSuccess` function in metrics:
    *   Accept an optional `duration_ms` parameter.
    *   Record the duration to a histogram when `duration_ms` is provided and metrics are initialized.
    *   Use attributes: `session.id`, `installation.id`, `user.email`, `user_tier`.
    *   Do nothing if metrics are not initialized.

*   Adjust the `setupUser` function:
    *   Call `logOnboardingSuccess` after successful onboarding.
    *   Pass the `config` and a new `OnboardingSuccessEvent` with the user's tier and measured onboarding duration.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.