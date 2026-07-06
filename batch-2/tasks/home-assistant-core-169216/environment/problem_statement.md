## Description

The UniFi Protect integration already supports alarm panels through the device's public API, but physical siren hardware is not yet exposed as Home Assistant entities. Users with UniFi siren devices have no way to control or monitor them — they cannot turn sirens on or off, set timed alerts, or adjust volume from Home Assistant, and the devices do not appear in the entity registry at all.

## Expected Behavior

- Siren devices accessible via the public API should appear as entities in Home Assistant, reflecting their current active or inactive state.
- If the public API is unavailable, no siren entities should be created.
- Users should be able to activate and deactivate sirens through standard Home Assistant turn-on and turn-off services.
- Activation should optionally accept a duration and a volume level. Only specific durations supported by the device are valid; requesting an unsupported duration should produce a clear validation error before any device communication occurs (meaning volume is not set and the siren is not activated).
- When a valid volume level is provided alongside activation, the volume should be applied before the siren starts.
- When a timed activation expires, the entity must automatically transition to the off state, since the device never broadcasts a stop event.
- Siren entities must become unavailable when the WebSocket connection drops and automatically recover when it reconnects.
- When a siren is removed from the system, its entity must reflect that by becoming unavailable.
- If HA restarts while a timed run is already in progress, the entity must still schedule the automatic turn-off rather than remaining stuck in the on state indefinitely.
- Underlying API errors (such as authorization failures or connection timeouts) must surface as Home Assistant errors rather than silent failures.

## Why This Matters

Users expect to be able to control every device in their UniFi Protect system from Home Assistant, including using sirens in automations, manually triggering alerts, and confirming the current alarm state. Without siren entity support, this class of hardware is completely invisible to the platform.
