## Description

We need a new set of components to enable overflow UI experiences in React Native. Currently, there is no standard utility for building layouts where a row of items automatically hides elements that don't fit within the available container width and surfaces those hidden items in a dropdown menu.

## Expected Behavior

- A container component that wraps a list of items and tracks which ones fit on screen and which ones overflow. The container should start hidden (opacity 0) while it performs initial layout calculations, then reveal itself once it knows which items to show.
- Individual item components that plug into the container, each identified by a unique string ID. Each item component should pass style and layout callbacks directly through to its child without adding extra wrapping elements.
- A hook that components can use to build a custom overflow menu. The hook should expose:
  - Whether the menu should be shown (including before layout is resolved — the menu should show by default before the first layout pass)
  - Which item IDs are currently hidden and should appear as menu options
  - A ref and layout callback to attach to the menu trigger element so the container can account for its size

## Why This Matters

Without these components, teams have no reusable primitive for building overflow experiences. With them, any component — buttons, tabs, or other elements — can be placed in an overflow container and the system will automatically manage which items are visible vs. hidden, surfacing hidden items in an accessible menu.
