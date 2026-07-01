# Fix Tab Navigation Order When Popover Is Open

## Description

When a popover is open on the page, keyboard users navigating with Tab encounter broken tab order behavior. Specifically, after tabbing through the popover's content and moving focus to elements after the popover trigger, continuing to tab forward through the rest of the page (including wrapping around from the last element back to the beginning) causes focus to land incorrectly inside the open popover rather than on the expected page element.

The root problem is that the popover's internal interactive elements remain part of the document's natural tab sequence even after the user has already moved focus away from the popover. This causes circular (wrap-around) navigation to break — instead of cycling back to the first element on the page, focus jumps into the popover unexpectedly.

## Expected Behavior

- When a user tabs out of an open popover to an element later in the page, the popover's internal elements should no longer be reachable via Tab.
- Circular forward tab navigation (wrapping from the last element back to the first) must work correctly when a popover is open — focus should go to the first element on the page, not back into the popover.
- Backward (Shift+Tab) circular navigation must also work correctly — tabbing backwards from outside the popover and wrapping around should allow focus to re-enter the popover from the last internal element.
- Tab and Shift+Tab from the boundary elements inside the popover should move focus correctly to the anchor/trigger or to the next element outside.

## Why This Matters

Users who rely on keyboard navigation should be able to use the page normally even when a popover is visible. The current behavior makes the page practically unusable for keyboard-only users once a non-modal popover is open, because the tab cycle becomes inconsistent and unpredictable.
