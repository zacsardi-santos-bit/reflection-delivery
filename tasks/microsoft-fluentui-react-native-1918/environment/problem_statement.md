## Description

The Badge component currently uses a fixed height for each size variant, which prevents the badge from expanding when its content needs more vertical space. This causes text or other content to overflow or get clipped unexpectedly. Additionally, the badge's text slot does not apply any explicit font styling, so its appearance is unpredictable and inconsistent across different usage contexts — it inherits whatever font properties happen to be in the surrounding environment.

## Expected Behavior

- The Badge container should use a minimum height instead of a fixed height, allowing it to grow taller when content requires it while still maintaining the intended design dimensions.
- The text displayed inside the Badge should always render with explicit, intentional font styling: a defined font family (from the design system's primary typography), a size appropriate to the badge's current size variant, a semibold weight, and horizontal padding around the text content.

## Why This Matters

Without these changes, badges in production may display clipped or overflowing text, and text inside badges may look inconsistent because no typography is enforced. Making height a minimum constraint and explicitly applying font tokens brings the Badge component in line with design system expectations and makes badge text predictable and visually consistent everywhere the component is used.
