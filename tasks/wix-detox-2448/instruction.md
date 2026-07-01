Implement a more flexible session configuration system for Detox by allowing auto-generation of server URLs and session IDs when not explicitly provided. Ensure proper validation of these values when specified, and introduce a configurable synchronization debugging timeout option.

*   Update `composeSessionConfig` function:
    *   Accept a fourth parameter `cliConfig` in addition to `detoxConfig`, `deviceConfig`, and `errorBuilder`.
    *   Return an object with fields: `autoStart`, `debugSynchronization`, `server`, and `sessionId`.
    *   Auto-generate `server` as `ws://localhost:<port>` and `sessionId` as a GUID when not provided.
    *   Source `sessionId` from `deviceConfig.session.sessionId`, fallback to `detoxConfig.session.sessionId`, and auto-generate if absent.
    *   Validate `sessionId` as a non-empty string, throwing `errorBuilder.invalidSessionIdProperty()` if invalid.
    *   Source `server` from `deviceConfig.session.server`, fallback to `detoxConfig.session.server`, and auto-generate if absent.
    *   Validate `server` as a WebSocket URL, throwing `errorBuilder.invalidServerProperty()` if invalid.
    *   Default `autoStart` to true if no server is configured, false if a server URL is provided, and allow explicit override.
    *   Default `debugSynchronization` to false, settable via `detoxConfig`, overridable by `deviceConfig`, and further by `cliConfig`.
    *   Validate `debugSynchronization` as a non-negative number, throwing `errorBuilder.invalidDebugSynchronizationProperty()` if invalid.

*   Modify `Client` class:
    *   Read `debugSynchronization` from the session config object passed to its constructor.

*   Update `DetoxConfigErrorBuilder`:
    *   Implement `invalidServerProperty()` to indicate invalid WebSocket URL for `session.server`.
    *   Implement `invalidSessionIdProperty()` to indicate `session.sessionId` should be a non-empty string.
    *   Implement `invalidDebugSynchronizationProperty()` to indicate `session.debugSynchronization` should be a positive number.

*   Create `isValidWebsocketURL` utility function:
    *   Located at `detox/src/utils/isValidWebsocketURL.js`.
    *   Return true for strings with `ws:` or `wss:` protocol, false otherwise.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.