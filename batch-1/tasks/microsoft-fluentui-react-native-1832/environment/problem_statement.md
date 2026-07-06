## Description

The shared text component in our design system does not wire up the accessibility tap gesture to the press action. When users of assistive technologies (such as screen readers on iOS) perform a double-tap to activate a text element that has a press handler, nothing happens — the action is silently ignored.

## Expected Behavior

- Text elements that have a press handler should respond to the accessibility tap gesture by invoking that press handler automatically.
- Developers should also be able to provide a custom accessibility tap callback if they need behavior that differs from the standard press action.
- All components built on top of the shared text element (such as tabs, menus, notifications, and badges) should automatically benefit from this fix without requiring individual changes to each component.

## Why This Matters

Screen reader users rely on accessibility tap gestures to interact with pressable elements. Without this wiring, an entire category of users cannot interact with text-based actions in the application. Fixing this at the shared text component level ensures consistent accessible behavior across the whole design system.
