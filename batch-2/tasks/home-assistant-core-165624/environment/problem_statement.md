## Description

The Roborock integration in Home Assistant does not support a newer family of Roborock robot vacuums (such as the Q10 S5+) that use a push-based communication protocol. These devices are different from older Roborock models and currently result in no vacuum entity being created in Home Assistant, even though the device appears in the user's Roborock account.

## Expected Behavior

- Newer Roborock devices using the push-based protocol should be registered as vacuum entities in Home Assistant with the correct entity ID and capabilities.
- The vacuum entity should support the standard vacuum controls: start, pause, stop, return to base, locate, fan speed control, and sending custom commands.
- Available fan speeds should include: off, quiet, balanced, turbo, max, and max plus.
- The entity state should reflect the device's real-time status (docked when charging, cleaning when actively cleaning, returning when heading back to the dock).
- Status updates should be pushed from the device and reflected immediately without needing to poll.
- If an invalid fan speed value or an unrecognized command is provided, a validation error should be raised.
- If the device fails to respond to a command due to a communication error, an appropriate error should be raised.
- When a manual refresh is requested from Home Assistant, the integration should request fresh status data from the device.
- The device should appear in diagnostics output with sensitive information (such as local key and device name) redacted.

## Why This Matters

Users with Q10-series Roborock vacuums are unable to integrate their device into Home Assistant automations and dashboards. Adding support for this device family allows these users to automate cleaning routines, monitor their robot vacuum's current state, and control it alongside other smart home devices.
