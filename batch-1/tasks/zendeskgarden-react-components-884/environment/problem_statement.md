## Description

The modals package currently supports standard Modal and TooltipModal components, but there is no DrawerModal component available. A DrawerModal is a common UI pattern that slides in from the side of the screen (typically the right side) and overlays the content. It's useful for displaying detailed information, forms, or secondary content without navigating away from the current page.

## Expected Behavior

- A new `DrawerModal` component should be available for import from the modals package
- The DrawerModal should slide in from the right side of the screen (or left side for RTL layouts)
- It should support the following compound components:
  - `DrawerModal.Header` - For the title section
  - `DrawerModal.Body` - For the main content area
  - `DrawerModal.Footer` - For action buttons
  - `DrawerModal.FooterItem` - For individual footer items
  - `DrawerModal.Close` - For the close button
- The drawer should lock page scrolling when open and restore it when closed
- The drawer should close when the user presses ESC, clicks the backdrop, or clicks the close button
- The component should support ref forwarding to all elements
- It should properly apply accessibility attributes based on an optional `id` prop

## Current Behavior

There is no DrawerModal component in the modals package. Developers must either implement this pattern from scratch or use third-party solutions, which may not integrate well with the existing Garden design system theming and styling.
