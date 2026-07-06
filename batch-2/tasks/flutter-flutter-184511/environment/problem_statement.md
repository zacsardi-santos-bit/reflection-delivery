## Description

When running the Flutter environment checker on a system where CocoaPods is not installed, the check currently reports it as a hard failure — marking the entire development environment as broken with an error-level message. This is too aggressive, because CocoaPods is only needed for building iOS and macOS targets, not for Flutter development targeting other platforms.

## Expected Behavior

- When CocoaPods is absent, the environment check should report a **partial** status rather than a fully broken/missing status.
- The corresponding message should be a **hint** rather than an error, indicating that something is optional or non-blocking rather than a critical problem.
- The message content should still guide users toward installation instructions.

## Why This Matters

Developers working on Android, web, or other non-Apple platforms are currently shown alarming "missing" errors when they simply haven't installed CocoaPods. This creates unnecessary confusion and makes the environment appear broken when it is perfectly functional for their use case. Downgrading this to a partial status with a hint message gives a more accurate and less alarming picture of the environment.
