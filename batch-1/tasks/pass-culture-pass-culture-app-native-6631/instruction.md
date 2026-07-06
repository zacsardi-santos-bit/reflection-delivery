Replace the existing boolean flags used for logging control in error classes with a unified enumeration that supports three distinct states: ERROR, INFO, and IGNORED. Update all relevant constructors and functions to utilize this new enumeration, ensuring a consistent and expressive API for error reporting.

*   Implement a `LogTypeEnum` in `src/libs/monitoring/errors.ts` with the following members:
    *   ERROR: for normal error capture.
    *   INFO: for info-level capture.
    *   IGNORED: to suppress all capture.

*   Update the `MonitoringError` class:
    *   Accept `logType: LogTypeEnum` in constructor options.
    *   Capture error message at info level when `logType` is `LogTypeEnum.INFO`.
    *   Suppress capture when `logType` is `LogTypeEnum.IGNORED`.

*   Update the `AsyncError` class:
    *   Accept `logType: LogTypeEnum` in constructor options alongside the `retry` option.

*   Update the `ScreenError` class:
    *   Accept `logType: LogTypeEnum` in constructor options alongside the `Screen` option.
    *   Suppress `captureException` when `logType` is `LogTypeEnum.IGNORED`.
    *   Capture at info level when `logType` is `LogTypeEnum.INFO`.

*   Update the `OfferNotFoundError` class:
    *   Accept `logType: LogTypeEnum` in constructor options.
    *   Use specific messages based on `logType` and `offerId` presence.

*   Update the `VenueNotFoundError` class:
    *   Accept `logType: LogTypeEnum` in constructor options.
    *   Use specific messages based on `logType` and `venueId` presence.

*   Modify `getEmailUpdateStatus` in `src/features/profile/helpers/useEmailUpdateStatus.ts`:
    *   Accept `{ logType: LogTypeEnum }` instead of `{ shouldLogInfo: boolean }`.
    *   Suppress Sentry capture for all errors when `logType` is `LogTypeEnum.IGNORED`.
    *   Capture at info level for 401 errors when `logType` is `LogTypeEnum.INFO`.

*   Create a `useLogTypeFromRemoteConfig` hook in `src/libs/hooks/useLogTypeFromRemoteConfig.ts`:
    *   Read `shouldLogInfo` from remote config context.
    *   Return `{ logType: LogTypeEnum.IGNORED }` when `shouldLogInfo` is false.
    *   Return `{ logType: LogTypeEnum.INFO }` when `shouldLogInfo` is true.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.