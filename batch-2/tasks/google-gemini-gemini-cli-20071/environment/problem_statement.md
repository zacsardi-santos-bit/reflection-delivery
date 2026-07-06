## Description

The CLI currently shows context window information as "X% context left" (remaining capacity). This framing is confusing, especially when the context window is fully used — the display reads "0% context left" rather than the more intuitive "100% context used." It should be changed to show how much context has been consumed instead of how much remains.

Additionally, when the CLI automatically compresses conversation history to free up context space, users receive no feedback about what happened. There should be an informational message telling them the before-and-after context usage percentages after a compression event, along with a hint pointing them to where they can adjust the compression threshold in settings.

Finally, settings with associated units (such as a threshold value that represents a percentage, or an interval that represents seconds) currently display as bare numbers. The units should be shown alongside the value so users can understand what the number means without having to look it up.

## Expected Behavior

- Context usage display shows "X% context used" (not "X% context left")
- On narrow terminals, the abbreviated form shows just the percentage without the label
- When fully consumed, shows "100% context used" rather than "0% context left"
- When 0% is consumed, shows "0% context used"
- After automatic history compression, an informational message reports the before and after context usage percentages and mentions where to change the compression threshold
- Context overflow warning messages reference the "context window limit" and show remaining tokens with a "left" qualifier
- Numeric settings with units display the value with its unit (e.g., a percentage setting shows both the raw decimal and the equivalent percentage; a seconds setting shows the number appended with the unit)

## Why This Matters

"Context used" is a more natural framing aligned with how users think about memory and storage consumption. Without feedback on compression events, users don't know that their context window was automatically managed. And without units on numeric settings, configuration values are ambiguous.
