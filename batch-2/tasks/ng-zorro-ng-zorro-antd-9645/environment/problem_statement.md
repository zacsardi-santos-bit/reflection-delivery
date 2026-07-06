## Feature Request: Advanced Character Counting for Input Wrapper

## Description

The input wrapper component should support a richer character counting feature, beyond a simple character display. Currently there is no way to:

- Configure a maximum character limit alongside the count display
- Apply a visual indicator when the user's input exceeds that limit
- Use a custom counting strategy (for example, treating emoji as a single character rather than multiple code units)
- Automatically trim the input value when it exceeds the maximum, so the field never goes out of range

## Expected Behavior

- When character counting is enabled and a maximum is configured, the count display should show the format "current / max" (e.g. "5/10").
- When the count exceeds the maximum, the wrapper should visually indicate the out-of-range state.
- Developers should be able to supply a custom counting function so that multi-byte characters like emoji are counted correctly as individual characters.
- Developers should optionally be able to provide a formatter function that, when the input exceeds the maximum, automatically trims the actual field value to fit within the limit — preventing the out-of-range state entirely.

## Why This Matters

Many applications need to enforce or display character limits on text inputs. The current component lacks these capabilities, forcing developers to implement counting logic outside the component. Supporting a configurable count config object directly on the wrapper component keeps the logic centralized and allows consistent handling of edge cases like emoji and multi-byte characters.
