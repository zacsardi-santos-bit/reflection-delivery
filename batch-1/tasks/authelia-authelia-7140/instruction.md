Implement a signal-listening service in Authelia to support log file reopening on receiving a hangup signal (SIGHUP) and enhance error messages for log file opening failures. This will facilitate seamless log rotation without service restarts.

*   Implement the `NewSignalService` function in `internal/commands/services.go`:
    *   Accept parameters: `name` (string), `action` (function returning error), `logger` (`*logrus.Logger`), and `signals` (variadic `os.Signal`).
    *   Return a `SignalService` object with the following methods:
        *   `Run()`: Block and listen for registered OS signals, invoking the `action` function upon signal receipt.
        *   `Shutdown()`: Ensure `Run()` exits within a reasonable timeout.
        *   `ServiceName()`: Return the `name` string.
        *   `ServiceType()`: Return the constant `serviceTypeSignal` ("signal").

*   Implement the `SignalService` struct in `internal/commands/services.go`:
    *   Ensure it conforms to the service interface with `Run()`, `Shutdown()`, `ServiceName()`, and `ServiceType()` methods.

*   Implement the `svcSignalLogReOpenFunc` function in `internal/commands/services.go`:
    *   Accept a `ServiceCtx` argument.
    *   Return `nil` if `ctx.GetConfiguration().Log.FilePath` is empty.
    *   Return a non-nil `SignalService` when the log file path is configured:
        *   The service should have `ServiceName()` return "log-reload" and `ServiceType()` return `serviceTypeSignal`.
        *   Listen for `SIGHUP` and reopen the log file upon receipt, creating a new timestamped log file.

*   Define the `ServiceCtx` interface in `internal/commands/services.go`:
    *   Embed `context.Context`.
    *   Declare methods: `GetLogger() *logrus.Logger`, `GetProviders() middlewares.Providers`, `GetConfiguration() *schema.Configuration`.

*   Modify `InitializeLogger` in `internal/logging/logger.go`:
    *   When a log file cannot be opened, prepend the error message with "error opening log file: " followed by the OS error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.