Implement telemetry for the user authentication and onboarding flow in the CLI. Record telemetry events when onboarding starts and completes successfully, including user tier information. Send these events to the clearcut structured logging backend and OpenTelemetry metrics pipeline.

*   Update the `setupUser` function in `packages/core/src/code_assist/setup.ts`:
    *   Change the second parameter to accept a `Config` object.
    *   Retrieve the validation handler internally using `config.getValidationHandler()`.
    *   Ensure the `UserData` interface includes an optional boolean field `hasOnboardedPreviously`, set to `false` for new users.

*   Create telemetry event classes in `packages/core/src/telemetry/types.ts`:
    *   `OnboardingStartEvent`:
        *   Constructor takes no parameters.
        *   `toLogBody()` returns 'Onboarding started.'.
        *   `toOpenTelemetryAttributes(config)` returns session attributes plus 'event.name' as `EVENT_ONBOARDING_START` and 'event.timestamp'.
    *   `OnboardingSuccessEvent`:
        *   Constructor accepts an optional `userTier` string.
        *   `toLogBody()` returns 'Onboarding succeeded. Tier: {userTier}'.
        *   `toOpenTelemetryAttributes(config)` returns session attributes plus 'event.name' as `EVENT_ONBOARDING_SUCCESS`, 'event.timestamp', and `user_tier`.

*   Define constants in `packages/core/src/telemetry/types.ts`:
    *   `EVENT_ONBOARDING_START` with value 'gemini_cli.onboarding.start'.
    *   `EVENT_ONBOARDING_SUCCESS` with value 'gemini_cli.onboarding.success'.

*   Update enums in `packages/core/src/telemetry/clearcut-logger`:
    *   Add `ONBOARDING_START` and `ONBOARDING_SUCCESS` to `EventNames`.
    *   Add `GEMINI_CLI_ONBOARDING_START` (192) and `GEMINI_CLI_ONBOARDING_USER_TIER` (193) to `EventMetadataKey`.

*   Extend `ClearcutLogger` in `packages/core/src/telemetry/clearcut-logger/clearcut-logger.ts`:
    *   `logOnboardingStartEvent(event: OnboardingStartEvent): void` logs an event with `EventNames.ONBOARDING_START` and metadata key `GEMINI_CLI_ONBOARDING_START`.
    *   `logOnboardingSuccessEvent(event: OnboardingSuccessEvent): void` logs an event with `EventNames.ONBOARDING_SUCCESS` and metadata key `GEMINI_CLI_ONBOARDING_USER_TIER`.

*   Add functions in `packages/core/src/telemetry/loggers.ts`:
    *   `logOnboardingStart(config: Config, event: OnboardingStartEvent): void`:
        *   Calls `ClearcutLogger.logOnboardingStartEvent(event)`.
        *   Emits an OTEL log with 'Onboarding started.' and relevant attributes.
        *   Calls `recordOnboardingStart(config)`.
    *   `logOnboardingSuccess(config: Config, event: OnboardingSuccessEvent): void`:
        *   Calls `ClearcutLogger.logOnboardingSuccessEvent(event)`.
        *   Emits an OTEL log with 'Onboarding succeeded. Tier: {userTier}' and relevant attributes.
        *   Calls `recordOnboardingSuccess(config, event.userTier)`.

*   Implement metrics functions in `packages/core/src/telemetry/metrics.ts`:
    *   `recordOnboardingStart(config: Config): void` records a start counter metric.
    *   `recordOnboardingSuccess(config: Config, userTier?: string): void` records a success counter metric with optional `user_tier`.

*   Export the following from `packages/core/src/telemetry/index.ts`:
    *   `OnboardingStartEvent`, `OnboardingSuccessEvent`, `logOnboardingStart`, and `logOnboardingSuccess`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.