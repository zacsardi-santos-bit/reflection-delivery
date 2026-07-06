## Description

When running a Flutter app via the flutter tool's interactive terminal mode, pressing the platform toggle shortcut ('o' or 'O') while the device's runtime service is not connected causes the tool to crash. This results in an unhandled error that disrupts the development session entirely.

## Expected Behavior

- Pressing the platform toggle key when the runtime service is unavailable should be handled gracefully — no crash or unhandled exception.
- The tool should display an informative status message to the developer explaining that the platform toggle is not available for the current device, so they understand why the action cannot be performed.

## Current Behavior

The flutter tool crashes with an unhandled error when the platform toggle shortcut is used and the device's runtime service connection is unavailable.

## Why This Matters

Developers working with devices that do not support the full runtime service protocol (or in situations where the service connection drops) should still be able to use the tool without it crashing. A graceful degradation with a helpful message is far preferable to an unexpected crash that ends the development session.
