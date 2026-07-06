## Description

The status command in the terminal UI has an awkward behavior where the status card written into terminal history shows a "refreshing" notice while a background rate-limit request is in progress, and then silently updates itself once the data arrives. Terminals are append-only by nature, so cards that appear to change retroactively after scrolling look broken and confusing.

Additionally, when the status command is run and the rate-limit API returns an empty or non-displayable result, the card shows a message like "data not available yet" — implying the data might appear soon. For accounts that simply don't have rate limit information, this is misleading.

## Expected Behavior

- Status cards written into terminal history should be permanently static — no "refreshing" notice should ever appear in a history card
- When a background refresh completes, the cached data should be stored and used the next time the user runs the status command (rather than updating the existing card)
- When the rate-limit API returns a response with no displayable data, the card should clearly say the limits are not available for that account type, regardless of whether a refresh is currently in progress
- Concurrent status commands each track their own background refresh independently, and the refresh tracking state is correctly cleaned up as each request completes

## Why This Matters

Users scrolling back through terminal history should see clean, complete output — not cards that appear to have changed since they were first written. The current behavior can make users think there is a bug or that the terminal is behaving unexpectedly. Accurate messaging for accounts without rate limit data also removes a source of confusion.
