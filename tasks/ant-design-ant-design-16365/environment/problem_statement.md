## Description

The collapsible panel component currently always places the expand/collapse arrow icon on the left side of panel headers. There is no built-in prop to move the icon to the right side — developers who want right-side icons must apply custom CSS overrides, which is a workaround rather than a supported feature. This is a commonly requested layout option that should be available as a first-class configuration.

## Expected Behavior

- The collapsible panel component should accept a new prop to control where the expand icon appears: either the left or right side of the panel header.
- The left position should remain the default so existing usage is unaffected.
- The chosen icon position should be reflected as a CSS class on the component's root element, making it straightforward to apply position-specific styles.
- A custom CSS prefix (configured globally or per-component) should be respected in the generated class name.
- The demo for the "extra content in panel header" example should be updated to showcase this new feature, including an interactive control that lets users switch between left and right icon positions.

## Why This Matters

Supporting icon position as a native configuration option removes the need for undocumented CSS hacks and aligns with how other layout options are handled in the component. It also makes the component documentation accurate — the existing FAQ entry explaining how to manually reposition the icon via custom styles can be removed in favor of the new prop.
