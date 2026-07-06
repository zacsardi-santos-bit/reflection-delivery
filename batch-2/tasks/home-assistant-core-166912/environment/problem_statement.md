# Add Diagnostics Support to Fresh-r Integration

## Description

The Fresh-r integration currently lacks support for the Home Assistant diagnostics system. When users run into problems with their Fresh-r ventilation devices, there is no easy way to capture a structured snapshot of the integration's state — including device information, sensor readings, and configuration — to share for troubleshooting purposes.

## Expected Behavior

- The Fresh-r integration should support the standard Home Assistant diagnostics interface for config entries.
- Requesting diagnostics should return a structured snapshot that includes:
  - Configuration entry details (with sensitive credentials like passwords automatically redacted)
  - A list of registered devices with their type, identifier, activation date, and any extra fields
  - A set of current sensor readings for each device, including temperature values, humidity, CO2 level, dew point, flow rate, and any extra readings
- The username field should be visible in diagnostics output while the password field should be hidden.

## Why This Matters

Without diagnostics support, users who encounter unexpected behavior from their Fresh-r integration cannot easily provide a meaningful bug report. Adding this capability allows users to share a safe, redacted snapshot of their integration state to help with troubleshooting.
