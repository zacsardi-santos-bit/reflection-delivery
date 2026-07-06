# Add Scene Support to XKNX

## Description

The XKNX library currently supports many KNX device types (lights, covers, switches, climate, etc.) but is missing support for KNX scenes. Scenes are a fundamental KNX feature that let users activate predefined environment configurations — for example, a "Romantic" lighting scene — by sending a single command over the KNX bus. Without scene support, users relying on this library cannot control or trigger KNX scenes programmatically.

## Expected Behavior

- A new device type should be available for representing KNX scenes, accepting a group address and a scene number as configuration.
- Activating a scene should send the encoded scene number to the configured KNX group address as a single-byte value.
- The device should support a "run" action via the generic action dispatch mechanism.
- Unrecognized actions should log a warning rather than raising an error.
- The device should be loadable from the YAML configuration file by specifying a group address and scene number under a dedicated scene section.
- The library must support encoding and decoding KNX scene numbers (valid range: 0 to 63). Values outside this range or of the wrong type must be rejected with a conversion error.

## Why This Matters

Scenes are widely used in KNX home automation to trigger complex lighting and environment configurations. Without first-class support in this library, users cannot easily integrate KNX scene control into their automations. This addition brings scene support to par with the other device types already supported by the library.
