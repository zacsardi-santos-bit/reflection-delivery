## Description

The Joy UI text input components do not properly forward certain event handlers that are passed through the slot customization API to the underlying native elements. Specifically, handlers for keyboard key-down and key-up events provided via slot props are silently ignored — they never fire when the user types in the field.

Additionally, the tab navigation component system has a bug where the first panel's content is not correctly shown by default when multiple panels are composed together, even though the first tab is active.

## Expected Behavior

- When a developer passes key-press or key-release event handlers through the slot customization API on a text input component, those handlers should fire when the corresponding keyboard events occur on the input.
- The same should work for the multi-line text area variant.
- Handlers passed through slot props should also be able to override top-level event handlers (e.g., a slot-level focus handler replaces the top-level focus handler).
- When tab panels are composed with tabs, the first panel should be visible by default and inactive panels should be hidden.

## Why This Matters

Developers using the slot customization API expect to have full control over the behavior of inner elements, including attaching keyboard event listeners. Without this, it's impossible to respond to keystrokes in the input using the recommended slot customization pattern. The broken tab default visibility also means that the basic use case of tabs — showing content for the active tab — doesn't work out of the box.
