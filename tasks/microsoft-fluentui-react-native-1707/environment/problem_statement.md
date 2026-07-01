## Description

The Avatar component is missing proper accessibility support for screen readers and other assistive technologies. When rendered, the root container does not declare its semantic role (as an image), is not explicitly marked as accessible, and provides no accessibility label. Additionally, the Avatar's inner elements are not excluded from the accessibility tree, which means screen readers may try to announce each internal child element individually instead of treating the whole Avatar as a single, coherent unit.

A similar issue exists with the Badge component: its displayed text is not explicitly marked as accessible.

## Expected Behavior

- The Avatar component's outer container should be marked as accessible, assigned an image role, and carry an accessibility label (defaulting to an empty string when none is provided).
- The Avatar component's inner container (background/initials area) should be excluded from the accessibility tree so assistive technologies do not announce it separately.
- The Badge component's text element should be explicitly marked as accessible.

## Why This Matters

Without these accessibility attributes, users who rely on screen readers or other assistive technologies cannot properly navigate or understand Avatar and Badge components. Fixing this ensures that these UI components are usable by everyone, and that screen readers present the Avatar as a meaningful, single image element rather than a collection of opaque sub-views.
