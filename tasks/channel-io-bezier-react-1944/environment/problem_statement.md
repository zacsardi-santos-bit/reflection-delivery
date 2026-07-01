## Description

The codebase currently has a set of layout components for arranging children in horizontal or vertical flex containers, along with a component for individual flex items. These components are being superseded by a newer generation of layout components, but the existing ones need to remain available for backward compatibility during a migration period.

To make this transition clear, the existing stack-layout components should be renamed to include a "Legacy" prefix, both in component names and file/folder organization. Additionally, the internal implementation should be modernized as part of the rename: instead of applying layout direction and alignment via inline styles, these should be expressed as CSS module classes. The CSS custom property names used for sizing and spacing should also be updated to use a consistent, design-system-specific prefixed naming convention.

## Expected Behavior

- A general-purpose flex-container layout component with the "Legacy" prefix must exist, supporting horizontal and vertical directions via a prop, and applying CSS module classes to reflect the chosen direction.
- Convenience wrapper components (also with the "Legacy" prefix) that always apply horizontal or vertical direction should exist and reflect that direction using the same CSS module classes.
- An individual flex-item component (also with the "Legacy" prefix) should be available, applying alignment overrides through CSS module classes rather than inline styles, and always setting all sizing and spacing CSS custom properties to non-empty values.
- When spacing is set on the container, only the second and subsequent valid element children should receive the spacing margin — non-element nodes at the start of the children list (such as conditional renders that evaluate to nothing or plain text nodes) must not count as the first child.
- All components must continue to support standard HTML customization props such as custom element type, inline styles, and additional class names.

## Why This Matters

As the design system matures, clearly marking older components as "legacy" helps developers understand which APIs are current and which are being phased out. This rename makes the migration path obvious and avoids confusion between legacy and modern layout components.
