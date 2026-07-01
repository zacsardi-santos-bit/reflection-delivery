## Description

The current gesture-related window event variants are named after specific hardware (touchpad), which ties them conceptually to a single platform and input device. This creates a problem as support grows for other platforms where the same gestures occur through different hardware (e.g., touchscreens on mobile devices). The hardware-specific names make the API feel inconsistent and create confusion for developers writing cross-platform gesture handling code.

## Expected Behavior

The gesture window events should be renamed to reflect the **action** being performed rather than the **hardware input** that triggers it:

- The two-finger pinch/magnification gesture event should be renamed to something that describes the gesture itself
- The double-tap smart magnification gesture event should be renamed to reflect the gesture type
- The two-finger rotation gesture event should be renamed to reflect the gesture action

The renamed events should preserve their existing field structure (device identifier, delta value, and touch phase where applicable) without any breaking changes to those fields.

## Why This Matters

Gesture events are platform-agnostic user interactions. By naming them after the action rather than the device, the same event names can be reused across platforms — including mobile platforms where touch gestures are triggered by fingers on a screen rather than a trackpad. This allows developers to write unified gesture-handling logic that works consistently regardless of the underlying platform.
