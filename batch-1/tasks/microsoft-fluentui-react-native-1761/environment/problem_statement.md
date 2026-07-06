## Description

The component library is missing a standardized notification banner component. Currently, developers building apps with this library have to implement notification UI inline in their own screens, which leads to inconsistent styling and behavior. We need a dedicated notification component that can be shared across apps.

## Expected Behavior

- There should be a notification component that accepts a visual style variant (such as informational/primary, neutral, danger, or warning) and renders accordingly.
- The component should display a main message (provided as child content) on the left side of the banner.
- The component should display a short action label (like "Undo" or "Sign in") aligned to the right side of the banner.
- The component's styling should be stable and consistent — it should not produce different visual results when re-rendered with the same props.

## Why This Matters

Having a shared, well-tested notification component ensures visual consistency across all apps using this library. It also removes the need for each team to build and maintain their own notification UI, reducing duplication and potential styling drift.
