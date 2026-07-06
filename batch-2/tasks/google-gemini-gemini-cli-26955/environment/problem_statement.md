## Description

The shell tool currently forwards every output chunk from a running command to the live display immediately, with no rate limiting. When a command produces rapid bursts of text output, this floods the UI with high-frequency updates that can overwhelm rendering and make the live view janky or unresponsive.

We need a throttled buffering mechanism for the live output stream. The first chunk should appear immediately so users know output is flowing, but subsequent rapid chunks should be batched and delivered at most once per update interval. If the command is so verbose that buffering all output would consume unbounded memory, only the most recent portion of the buffer (up to a defined character limit) should be retained — older content can be safely discarded from the live view (the final complete output is still used for the LLM response).

When a command finishes or goes silent for longer than the update interval, any buffered output must be flushed automatically so nothing is silently dropped.

## Expected Behavior

- First text chunk is displayed immediately upon arrival.
- Subsequent rapid text chunks are accumulated and sent at most once per throttle interval.
- The live buffer is bounded: if accumulated text exceeds the maximum size, the oldest content is dropped, keeping only the most recent characters within the limit.
- The buffer truncation must not split a Unicode surrogate pair — if the computed cut point falls on a low surrogate, the cut advances by one character.
- Terminal (PTY/ANSI) output snapshots are never throttled — each snapshot is forwarded immediately since it already represents the full current screen state.
- When the command exits, any pending buffered text is flushed before the tool finishes.
- A trailing flush also fires automatically after the command goes silent for the throttle interval.
- When the command exits and immediately flushes, any pending timer-based trailing flush is cancelled to avoid a duplicate call.

## Why This Matters

Without this change, long-running commands with high-frequency output produce a storm of UI updates that degrade the interactive experience. The throttle keeps the display responsive while ensuring no output is lost at the end of the command.
