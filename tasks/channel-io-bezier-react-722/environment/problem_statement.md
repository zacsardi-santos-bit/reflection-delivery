## Description

The navigation group component in the Navigator does not currently support an active/selected state. When a user is viewing a section that corresponds to a navigation group, there is no way for the application to visually indicate that the group is active. Every group looks identical regardless of whether its content is currently being displayed.

## Expected Behavior

- The navigation group component should accept a boolean property indicating whether it is currently active
- When the component is active, the group header should render with a highlighted visual style that clearly distinguishes it from other groups
- When the component is not active, the group header should render in its default appearance, with hover and focus-visible interaction states
- The component should render any content provided for the right side of the group header

## Why This Matters

Navigation components need to communicate state to users. Without an active indicator, users cannot tell which navigation group corresponds to the section they are currently viewing. Adding this capability makes the navigator component more complete and usable in applications where selected state matters for orientation.
