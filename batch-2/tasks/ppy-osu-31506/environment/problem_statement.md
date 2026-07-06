## Description

When a player dims the background fully during gameplay (so the storyboard becomes completely invisible), the storyboard currently stops receiving internal update ticks from the engine. This is because the engine skips updating invisible components as an optimization.

The consequence is that when the player later reduces the dim level (making the storyboard visible again), the storyboard has a large amount of animation and state work to "catch up" on all at once, causing a noticeable stutter or jump in the storyboard animations.

## Expected Behavior

- Even when a storyboard is fully dimmed and therefore invisible, it should continue to run its internal update loop.
- When the storyboard becomes visible again (by reducing the dim level), it should already be in sync with the game clock with no catch-up work needed.
- The storyboard should remain present (in terms of receiving engine updates) whenever the storyboard show setting is enabled, regardless of whether it is currently visible on screen.

## Why This Matters

This affects players who use high dim settings during gameplay but still have storyboards enabled. Without this fix, any time the dim is reduced (e.g., at the start of a break section), the storyboard will stutter as it processes a backlog of animation work. The fix ensures smooth visual behavior when transitioning between dimmed and undimmed states.
