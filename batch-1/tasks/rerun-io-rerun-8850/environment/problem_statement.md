## Description

Some video decoders require additional samples to be queued ahead of the currently requested playback position before they can start producing output frames. For example, certain codec implementations in specific environments need a buffer of extra samples beyond what the player currently submits — without this look-ahead, those decoders never produce any output.

There is currently no standardized way for an individual decoder to communicate how much look-ahead data it requires. The video player has no mechanism to ask a decoder "how many extra samples do you need queued?", so it can't adapt its submission strategy per decoder.

## Expected Behavior

- The decoder abstraction should expose a way for implementations to declare the minimum number of samples they need enqueued beyond the currently requested sample before they can produce output.
- The default behavior (for decoders that have no special requirements) should be to require zero extra samples, so existing decoder implementations work without any modification.
- Decoder implementations that do need additional look-ahead can override the default to return the appropriate count.

## Why This Matters

Without this mechanism, certain decoder types silently fail to produce frames for some video files, with no clear reason why. Making look-ahead requirements explicit per decoder allows the video player to automatically adapt its queuing strategy and ensures smooth playback across different decoder implementations and environments.
