Implement a logging feature for the experimental debug adapter in the Python extension for VS Code. Ensure that debug session traffic can be optionally logged to a file when enabled in the session configuration. Update the necessary components to support this logging capability.

*   Export `DebugSessionLoggingFactory` from `src/client/debugger/extension/adapter/logging.ts`.
    *   Constructor must accept a `FileSystem` service argument.
*   Export `IDebugSessionLoggingFactory` symbol/interface from `src/client/debugger/extension/types.ts`.
*   Update `DebugAdapterActivator` in `src/client/debugger/extension/adapter/activator.ts`.
    *   Constructor must accept `IDebugSessionLoggingFactory` as the third parameter.
    *   In `activate()`, if the debug adapter experiment is active:
        *   Call `debugService.registerDebugAdapterTrackerFactory('python', loggingFactory)` once.
        *   Call `debugService.registerDebugAdapterDescriptorFactory('python', descriptorFactory)` once.
        *   Push both return values to the disposable registry.
    *   If the experiment is not active, do not call the register methods.
*   Implement `DebugSessionLoggingFactory.createDebugAdapterTracker(session)`.
    *   Do not create a write stream if `session` lacks a `logToFile` configuration property.
    *   Do not create a write stream or write to any stream if `session.configuration.logToFile` is false.
    *   Create a write stream at `path.join(EXTENSION_ROOT_DIR, 'debugger.vscode_{session.id}.log')` using the `FileSystem` service if `session.configuration.logToFile` is true.
*   When `logToFile` is true, ensure the tracker returned by `createDebugAdapterTracker`:
    *   Implements `onWillStartSession()` and writes 'Starting Session' to the stream.
    *   Implements `onDidSendMessage(message)` and writes 'Client <-- Adapter' with the message's type to the stream.
    *   Implements `onWillReceiveMessage(message)` and writes 'Client --> Adapter' with the message's type to the stream.
    *   Implements `onWillStopSession()` and writes 'Stopping Session' to the stream.
    *   Implements `onError(error)` and writes 'Error' to the stream.
    *   Implements `onExit(code, signal)` and writes 'Exit-Code: {code}' and 'Signal: {signal}' to the stream. Use 0 if code is undefined and 'none' if signal is undefined.
*   Ensure that when all lifecycle methods are called once for a logging-enabled session, the total number of write calls to the stream is exactly 7.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.