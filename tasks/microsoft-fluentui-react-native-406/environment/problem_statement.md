## Description

The ContextualMenu component does not currently expose any controls for focus behavior when the menu opens. There is no way for consumers to configure whether the menu automatically acquires keyboard focus on mount, or whether the menu's inner container element should be keyboard-accessible and focusable separately.

## Expected Behavior

- The ContextualMenu should support a new optional boolean setting that controls whether it automatically receives focus when it appears. This should default to enabled (the menu focuses on mount by default).
- The ContextualMenu should support a second optional boolean setting that controls whether the inner container element accepts keyboard focus and accessibility interactions. This should default to disabled.
- When rendered with all defaults, the component's output should reflect both of these default behaviors: the callout receives initial focus, and the container element does not independently accept keyboard or accessibility focus.
- The inner container element (a standard view) should wrap the menu's children and sit between the callout root and the menu items in the component tree.

## Why This Matters

Without these settings, developers have no way to control focus behavior for the contextual menu, which can cause accessibility and keyboard navigation issues in applications that need fine-grained focus management. Adding these options with sensible defaults lets teams use the component as-is for the common case while still supporting advanced focus control scenarios.
