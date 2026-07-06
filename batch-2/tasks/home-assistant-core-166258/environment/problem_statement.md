# Add Battery Triggers Support

## Description

The battery component currently only supports automation **conditions** — you can check whether a battery is low at the moment an automation runs, but you cannot have an automation fire automatically the moment a battery changes state. This means users have to build workarounds using generic state-change triggers and manually filtering by device class, rather than having a clean, purpose-built battery trigger.

## Expected Behavior

The battery component should support automation triggers so users can react to battery state changes in real time:

- Trigger when one or more batteries become low (or recover from low)
- Trigger when one or more devices start or stop charging
- Trigger when a battery level changes numerically
- Trigger when a battery level crosses a configured percentage threshold

These triggers should work with binary sensor entities (for low/charging state) and with numeric sensor and number entities (for battery percentage). They should be available as a labs/preview feature.

Each trigger should support a "behavior" option to control how multi-device groups are handled: fire when **any** device in the group meets the condition, only when the **first** one does, or only when the **last** one does.

## Why This Matters

Without dedicated battery triggers, users cannot easily build automations like "notify me when my smoke detector battery goes low." Adding trigger support to the battery component completes the feature set and makes it consistent with other device-class integrations that provide both conditions and triggers.
