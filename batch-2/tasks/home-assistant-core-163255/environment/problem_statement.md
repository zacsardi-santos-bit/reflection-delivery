## Description

The Smarla baby rocker integration does not support firmware updates. Users who have the device connected to Home Assistant have no way to see the currently installed firmware version, check whether a newer firmware is available, trigger an update, or monitor installation progress — all from within the Home Assistant interface.

## Expected Behavior

- The integration should expose a firmware update entity that shows the currently installed firmware version.
- When a newer firmware version is available, the entity should indicate that an update is ready.
- Users should be able to initiate a firmware update through the standard Home Assistant update interface.
- While an update is downloading or installing, the entity should reflect that progress is underway.
- If the firmware update status cannot be determined (e.g., connectivity issues), the entity should enter an unknown state rather than showing stale data.

## Why This Matters

Without firmware update support, users must manage device firmware outside of Home Assistant. Adding this capability gives users a unified interface to keep their device up to date, with proper status feedback during the update process.
