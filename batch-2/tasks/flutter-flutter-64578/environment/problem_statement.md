## Description

Flutter's code size analysis feature lets developers inspect how their app binary is composed, but it currently doesn't report any usage analytics. This means the Flutter team has no visibility into how often developers are using the feature or which platforms they're analyzing.

## Expected Behavior

- When a developer runs a build with code size analysis enabled on any supported desktop platform (Linux, macOS, or Windows), an analytics event should be recorded indicating which platform was analyzed.
- When a developer performs an Android APK build with code size analysis, an analytics event should likewise be recorded for the APK platform.
- The analytics object responsible for sending these events should be provided to the size analysis component so it can dispatch the event upon completing its analysis.

## Why This Matters

Without usage analytics, it's impossible to know whether the code size analysis feature is being adopted and on which platforms it is most useful. Adding analytics telemetry for this feature will help the Flutter team make data-driven decisions about future improvements.

The core size analysis utility needs to accept an analytics/usage reporting object as part of its configuration, and the build commands for each platform need to wire in the global usage reporting service so the events are dispatched correctly when analysis is run.
