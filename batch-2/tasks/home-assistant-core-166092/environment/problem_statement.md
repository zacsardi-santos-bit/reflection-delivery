## Description

SMLIGHT Ultima devices include an integrated ambient LED strip that can display colors and animated lighting effects. Currently the Home Assistant SMLIGHT integration has no way to expose or control this hardware feature, so users cannot incorporate it into automations, scenes, or dashboards.

## Expected Behavior

- Ultima devices should expose a controllable light entity that supports:
  - Turning the LED strip on and off
  - Adjusting brightness
  - Setting an RGB color
  - Selecting from a set of built-in lighting effects (Solid, Off, Blur, Rainbow, Breathing, Color Wipe, Comet, Fire, Twinkle, Police, Chase, Color Cycle, Gradient Scroll, Strobe, System Warning, System Error, System OK, System Info)
- Non-Ultima devices (those without a built-in LED strip) should not have a light entity created
- State changes reported by the device over its real-time event stream should automatically update the entity in Home Assistant
- Invalid or unknown lighting modes received from the device should be handled gracefully without errors
- Connection errors during control commands should surface as a standard Home Assistant error
- After recovering from a connection error, the entity should resume normal operation

## Why This Matters

Users with SMLIGHT Ultima hardware currently have no way to control the ambient LED strip through Home Assistant. Adding a light entity allows them to include the LED strip in automations (e.g., flash red on an alert) and control it alongside other lights in their home.
