## Description

The Hue BLE integration currently only supports being set up via automatic Bluetooth discovery, which creates a separate setup flow for each nearby compatible device detected by the scanner. There is no way for users to manually initiate the setup process themselves. This is limiting because it puts the user entirely at the mercy of automatic discovery timing and offers no way to select which device to configure.

## Expected Behavior

- Users should be able to manually start the setup process for Hue BLE lights and be presented with a list of all nearby compatible devices discovered via Bluetooth scanning.
- The device selection list should only show devices that are actually compatible with the integration — unrelated Bluetooth devices nearby should be filtered out and not appear in the list.
- If no compatible devices are found at the time the user starts setup, the flow should immediately inform them that no devices were found rather than showing an empty list.
- If a device the user selects has already been configured, the flow should inform them of this before creating a duplicate entry.
- Automatic Bluetooth discovery-triggered setup flows should be disabled. When the system's Bluetooth scanner detects a compatible device, it should no longer automatically open a setup flow — instead users should use the manual setup path.

## Why This Matters

The change gives users direct control over when and which device to configure, while removing the confusing automatic flows that would appear unprompted. It also requires updating the localization strings to reflect the new flow names and reasons.
