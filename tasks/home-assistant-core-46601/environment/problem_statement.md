## Description

The Z-Wave JS integration currently exposes no WebSocket API endpoints for managing the driver's logging configuration. Developers and power users who want to diagnose Z-Wave network issues have no way to programmatically retrieve or change logging settings — such as verbosity level, whether logging is active, whether to write logs to a file, or forcing output to the console.

## Expected Behavior

- A new WebSocket command should allow clients to **retrieve** the current Z-Wave JS driver log configuration, returning all relevant settings (enabled state, verbosity level, file logging settings, console forcing).
- A new WebSocket command should allow clients to **update** the log configuration. At least one setting must be provided per request.
- The update command should validate inputs carefully:
  - Unrecognized verbosity levels should be rejected with an informative error.
  - An update request with no settings provided should be rejected.
  - Enabling file logging without specifying a log file path should be rejected with an informative error.

## Why This Matters

Being able to dynamically configure Z-Wave JS logging remotely through the WebSocket API makes it much easier to debug Z-Wave network problems without requiring a restart or direct file system access. This is especially useful in environments where log level or output destination needs to be adjusted at runtime.
