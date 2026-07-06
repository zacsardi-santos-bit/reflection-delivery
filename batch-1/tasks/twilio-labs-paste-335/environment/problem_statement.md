## Description

The icon components used across the design system need to be updated with new, refreshed artwork. Several icons — including those used to indicate errors, information, warnings, close/dismiss actions, and loading state — have new visual designs that should replace the existing ones. At the same time, the container element that wraps all icons should be changed to use a flexible inline layout, which provides better cross-browser rendering consistency compared to the current basic inline display approach.

## Expected Behavior

- The error icon displayed in error-variant alerts should show the new icon shape
- The information icon displayed in neutral-variant alerts should show the new icon shape
- The warning icon displayed in warning-variant alerts should show the new icon shape
- The close/dismiss icon shown in dismissible alerts should show the new icon shape
- The loading spinner icon shown in buttons with a loading state should show the new icon shape
- Icon container elements across all icon components should use an inline flexible box layout (with appropriate cross-browser vendor prefix fallbacks) instead of the current basic inline display approach

## Why This Matters

These icon updates bring the design system in line with a refreshed icon set that better meets visual design standards. The change to an inline flexible display for the icon container also ensures consistent cross-browser alignment behavior when icons appear alongside other inline content.
