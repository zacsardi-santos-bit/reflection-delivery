## Description

When a user drags a sliding panel to a new position on screen — for example, partially swiping a bottom sheet downward — and then the panel is programmatically dismissed, the slide-out animation should start from wherever the panel actually is at that moment. Currently it ignores any drag offset the panel may have accumulated and animates from the panel's original, undragged position, causing a visible jump before the exit animation begins.

## Expected Behavior

- When the Slide component exits, it should read the child element's current on-screen position, including any translation already applied (e.g., from a drag gesture), and factor that into the exit animation's starting point.
- The exit translation should be computed relative to the element's actual current position rather than its resting position.
- A utility should be available that can parse any common CSS transform string format — including matrix, 3D matrix, two-axis translate, three-axis translate, and single-axis translate variants — and return the numeric X and Y translation values from it. For unrecognized, empty, or "none" transforms, it should return zero for both axes.

## Why This Matters

Without this fix, dismissing a dragged panel produces a jarring animation glitch. With it, the dismissal animation is visually continuous and correct regardless of how far the panel has been dragged before being closed.
