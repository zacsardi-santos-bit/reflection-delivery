## Description

Flutter's web tooling should support Microsoft Edge (the modern Chromium-based version) as a discoverable and usable web development target on Windows. Currently, Edge never appears in the list of available web devices, even on Windows systems where a compatible version of Edge is installed. This means developers cannot select Edge for running or testing their Flutter web applications through the standard device workflow.

## Expected Behavior

- On Windows, the tool should automatically detect whether a sufficiently modern (Chromium-based) version of Edge is installed by querying the installed version from the system.
- If the installed version meets the minimum requirement for Chromium-based Edge, it should appear in the list of available web devices.
- If the installed version is too old (predating the Chromium transition), Edge should not be listed.
- On Linux and macOS, Edge should never appear in the device list since it is not supported on those platforms.
- If Edge is not installed or cannot be launched, it should not appear in the device list.

## Why This Matters

Developers building Flutter web apps want to test across multiple browsers. Edge is a major browser on Windows and, now that it's based on the same engine as Chrome, it is a legitimate and useful deployment target. Exposing it as an available device gives developers a convenient way to run and debug their apps in Edge without extra manual configuration.
