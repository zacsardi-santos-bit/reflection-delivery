## Description

This is a patch release fixing several independent bugs across different Home Assistant integrations.

## Issues Fixed

### Audio MIME Type Parsing
An audio stream type parser failed when a connected device advertised its audio format using lowercase characters (e.g., lowercase "l" instead of uppercase "L" in the audio subtype). This caused audio processing to silently fail for those devices, even though the format was otherwise valid. The parser must handle both uppercase and lowercase variants identically.

### Local-Only User Bypass via Signed URLs and WebSockets
Users configured as "local-only" (restricted to the local network) could bypass that restriction by accessing the system through specially signed URLs or WebSocket connections from remote internet addresses. Neither the signed URL handler nor the WebSocket authentication layer enforced the local-network restriction. Additionally, inactive user accounts were not rejected when accessing signed URLs. WebSocket connections from remote addresses must now clearly communicate the reason for rejection.

### IMAP Connection Loss — Missing Diagnostic Log
When the system lost contact with an IMAP mail server during a push-mode idle session, it failed to log any information about the pending idle session being canceled. This made it very difficult to diagnose connectivity problems. A diagnostic message identifying the affected server must be logged when the idle wait is canceled.

### MQTT Light Crashes After Restart
An optimistic MQTT light that was saved in the off state would fail when turned on after a restart. The saved state stored a null color mode, and on restore that null value overwrote the properly initialized color mode, causing the turn-on operation to report that the light does not have a valid color mode. The restore logic must not overwrite an initialized color mode with a null value.

### Roborock Vacuum — Unhelpful Fan Speed Error
Roborock vacuum cleaners (standard and Q7 series) raised an unhelpful internal error when given an unsupported fan speed value, instead of a clear validation message. Users deserve to see a descriptive validation error that identifies exactly which value was invalid.

### Victron BLE — False Reauthentication Prompts
Certain Victron Energy Bluetooth devices broadcast advertisement packets with mode bytes that the parser does not recognize. The system was incorrectly counting these unreadable packets as authentication failures, eventually triggering unnecessary reauthentication prompts even when the encryption key was perfectly valid. Unrecognized-mode advertisements must be treated as neutral — they should not affect the failure counter in either direction.

### Gardena Bluetooth — Incorrect Sensor Metadata
The "Current distance" and "Current flow" Gardena Bluetooth sensors were missing the correct measurement state class, and the "Overall flow" sensor was using an incorrect device class (volume instead of water).

## Expected Behavior

- Audio MIME type parsing works regardless of letter case in the subtype
- Local-only users are rejected from remote addresses when using signed URLs or WebSocket connections, with a clear error reason on WebSocket
- Inactive users are rejected when accessing signed URLs
- IMAP idle cancellations are logged with the server name
- Optimistic MQTT lights restore properly from a saved null color mode and can be turned on without error
- All Roborock vacuum types raise a descriptive validation error for invalid fan speeds
- Victron BLE devices with unrecognized advertisement modes do not trigger false reauthentication
- Gardena Bluetooth sensors report correct state classes and device classes
