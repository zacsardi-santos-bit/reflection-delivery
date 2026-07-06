I'm seeing a bug where the fullscreen button on several visual components doesn't actually work when clicked.

*   When the fullscreen button is configured to appear in the AnnotatedImage component and a value is set, clicking the button labeled 'Fullscreen' must cause the button labeled 'Exit fullscreen mode' to become visible (and 'Fullscreen' to disappear), and clicking 'Exit fullscreen mode' must restore the 'Fullscreen' button.

*   When the fullscreen button is configured in the Image component running in interactive mode with a value set, clicking 'Fullscreen' must toggle to display 'Exit fullscreen mode', and clicking 'Exit fullscreen mode' must toggle back to display 'Fullscreen'.

*   When the fullscreen button is configured in the ImageSlider component and preview images are present, clicking 'Fullscreen' must toggle to display 'Exit fullscreen mode', and clicking 'Exit fullscreen mode' must toggle back to display 'Fullscreen'.

*   When the fullscreen button is configured in the NativePlot component, clicking 'Fullscreen' must toggle to display 'Exit fullscreen mode', and clicking 'Exit fullscreen mode' must toggle back to display 'Fullscreen'. The chart must continue to render properly after toggling.

*   The fullscreen state must be tracked locally within each component so that the button label updates reactively: 'Fullscreen' when not in fullscreen mode and 'Exit fullscreen mode' when in fullscreen mode. These aria-label strings must be used exactly as specified.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.