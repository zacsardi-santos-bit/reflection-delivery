## Description

Authelia writes logs to a configurable file path, but it currently has no way to reopen that log file at runtime. This means that when system administrators use log rotation tools (which typically move or compress the existing log file), Authelia keeps writing to the old (moved) file handle. The only way to make Authelia start writing to a fresh log file is to restart the entire service.

## Expected Behavior

- Authelia should listen for a hangup signal (the standard Unix mechanism for instructing a daemon to reload its configuration or reopen files) when a log file path is configured.
- When that signal is received, Authelia should close and reopen the log file, creating a new timestamped log file so that subsequent log entries go to the new file.
- A dedicated "log-reload" service should be registered for this purpose, but only when a log file path is actually configured. When no log file path is set, no such service should be created.
- When a log file cannot be opened, the error message returned should clearly indicate that the failure was specifically a log file opening problem, rather than returning a bare OS-level error.

## Why This Matters

Log rotation is a standard operational concern for long-running services. Without runtime log file reopen support, log rotation for Authelia is disruptive and requires downtime. Administrators using tools like logrotate expect to send a hangup signal and have the service seamlessly continue writing to a fresh log file. The improved error message also makes diagnosing misconfigured log paths much easier.
