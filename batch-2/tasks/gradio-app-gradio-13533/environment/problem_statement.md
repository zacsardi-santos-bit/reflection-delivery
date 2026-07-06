## Description

The fullscreen button is broken in several components: the annotated image display, the interactive image upload widget, the image comparison slider, and native chart components. When users click the fullscreen button, nothing happens — the button label does not change and fullscreen mode is not actually toggled.

## Expected Behavior

- Clicking the fullscreen button should enter fullscreen mode and update the button label to indicate the user can exit.
- Clicking the button again should exit fullscreen mode and restore the original button label.
- This toggle behavior should work correctly in all four affected components: annotated image, interactive image, image slider, and native plot.

## Current Behavior

The fullscreen button renders and is visible, but clicking it has no effect. The component's fullscreen state is never updated in the UI, so the button label stays unchanged regardless of how many times it is clicked.

## Why This Matters

Users clicking the fullscreen button expect to be able to view images and charts in a larger, fullscreen view. Since the toggle does not work, this feature is effectively non-functional in these components. Fixing this ensures the button behaves as expected — entering and exiting fullscreen mode on each click.
