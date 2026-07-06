I'm working on our Angular input wrapper component and I need to beef up its character counting. Right now it can show a basic count when the show-count toggle is on, but that's it, and I keep having to reimplement limit logic outside the component every time an app needs to enforce or display a max on a text field. I want to centralize all this so the wrapper handles the edge cases itself.

Here's what I'm after. I need to configure a maximum character limit alongside the count display, and when both counting is enabled and a max is set the display should render as "current/max" (so something like "5/10"). When the actual count goes over that max, the wrapper should apply an out-of-range visual indicator to signal the exceeded state.

I also need a custom counting strategy so emoji get counted as a single character instead of multiple code units, that should be a function taking the input string and returning a number, configurable per instance.

Oh and one more thing, an optional exceed formatter function that, when the value goes past the max, automatically trims the real field value back down to fit within the limit. When that formatter's in use the out-of-range indicator shouldn't show at all since the value's kept in bounds automatically.

All of this should be expressible as a single typed count config object bound to the wrapper alongside the existing show-count toggle, so the counting, the max, the custom counter, and the exceed formatter all live in one place.
