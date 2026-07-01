Refactor the telemetry gating system by implementing a new analytics wrapper type that manages event buffering and forwarding based on telemetry preferences. Ensure the logging setup function directly accepts an analytics object and modify event handling to remove telemetry flags from payloads.

*   Update `setupLoggerAndTelemetry` function:
    *   Accept a `MongoshAnalytics` object directly as the third parameter.
    *   Handle `mongosh:new-user` and `mongosh:update-user` events with only a `userId` argument.
    *   Ensure the log entry for `mongosh:update-user` has `msg` equal to 'User updated' without an `enableTelemetry` field.
    *   Add a handler for `mongosh:globalconfig-load` event to log 'Loading global configuration file' with `filename` attribute from the event payload.

*   Implement `MongoshAnalytics` interface in `packages/logging/src/analytics-helpers.ts`:
    *   Define methods `identify(info: any): void` and `track(info: any): void`.

*   Implement `ToggleableAnalytics` class in `packages/logging/src/analytics-helpers.ts`:
    *   Accept a `MongoshAnalytics` instance in the constructor.
    *   Start in a paused state, buffering `identify()` and `track()` calls.
    *   Implement `enable()` to flush buffered events to the target and forward subsequent calls immediately.
    *   Implement `pause()` to resume buffering of events.
    *   Implement `disable()` to discard buffered events, ensuring they are not replayed upon re-enabling.

*   Ensure analytics events are emitted without conditional checks on a telemetry flag; the caller manages telemetry toggling.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.