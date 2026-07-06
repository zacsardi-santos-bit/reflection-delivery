## Description

When CLI output is too long to display in the terminal, the UI shows a truncation indicator telling the user how many lines are hidden. However, the indicator only shows a count — it gives no hint that the user can press a keyboard shortcut to expand and view the full content. Users may not realize the feature exists, reducing its discoverability.

## Expected Behavior

- When content is truncated, the truncation indicator should include a hint about the keyboard shortcut for showing hidden lines, so users know they can take action.
- In a normal-width terminal, the full hint should appear alongside the line count, e.g. indicating both how many lines are hidden and what key to press to reveal them.
- In a narrow-width terminal where space is limited, a shorter hint format should be used that still communicates the keyboard shortcut without taking up too much space.
- The singular/plural form of "line" vs "lines" should be preserved based on the hidden line count.

## Why This Matters

Users who see truncated output may not know there is a way to expand it. Adding an inline keyboard shortcut hint improves the discoverability of the overflow navigation feature and reduces friction for users who want to see the full output without consulting documentation.
