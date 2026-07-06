## Description

The Android TV integration's setup flow gates certain connection options (ADB server address, ADB server port, and ADB key file path) behind an "advanced options" toggle. Users who don't know to enable this toggle have no way to configure their ADB connection fully during initial setup.

These options should be surfaced in the standard setup form for everyone, grouped into a collapsible section, rather than hidden behind an advanced mode.

## Expected Behavior

- All ADB-related options (key path, server IP, server port) are accessible during the normal setup flow without any special mode or context flag.
- User-submitted data that includes these grouped options is stored in the same flat format the integration has always used — existing configurations remain fully compatible.
- All existing validation and error handling (conflicting options, invalid values, connection failures) continues to function correctly.

## Why This Matters

Users setting up Android TV devices with a custom ADB server or a specific ADB key file should not need to discover and enable a hidden advanced mode just to configure their connection. Surfacing these options as a collapsible section in the standard flow makes setup simpler, more discoverable, and consistent with other integrations.
