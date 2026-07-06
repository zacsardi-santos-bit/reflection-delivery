## Description

The Xthings Cloud integration currently only exposes lighting devices to Home Assistant. However, Xthings also makes smart plugs and other switchable devices that are accessible through the same cloud service. These devices cannot be controlled or monitored through Home Assistant because no switch platform support exists in the integration.

## Expected Behavior

- Smart plug and switch-type devices from Xthings should appear as switch entities in Home Assistant under the switch domain.
- Turning a switch entity on or off should send the appropriate command to the Xthings cloud API, with different API calls depending on whether the device is a plug or a generic switch.
- If a device is offline, its corresponding switch entity should show as unavailable.
- The state of switch entities should update in real time when devices are toggled externally, using the existing websocket connection.
- Existing light entity tests should remain unaffected by the addition of the switch platform.

## Why This Matters

Users with Xthings smart plugs or other switchable devices currently have no way to integrate those devices with Home Assistant through the Xthings Cloud integration. Adding switch platform support makes the integration fully useful for households that have a mix of Xthings lighting and plug/switch devices.
