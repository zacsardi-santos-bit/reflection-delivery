Implement a throttled buffering mechanism for the live output stream in the shell tool to manage high-frequency command outputs effectively. Ensure the first output chunk is displayed immediately, while subsequent chunks are batched and delivered at most once per update interval. Maintain a bounded buffer to prevent excessive memory usage.

*   Export the constant `LIVE_OUTPUT_MAX_BUFFER_CHARS` from `packages/core/src/tools/shell.ts`.
    *   This constant defines the maximum number of characters to retain in the live output buffer.

*   Display the first text chunk immediately upon arrival by calling `updateOutput` without delay.

*   Accumulate subsequent text chunks arriving within `OUTPUT_UPDATE_INTERVAL_MS` in a buffer.
    *   Do not forward these chunks to `updateOutput` immediately.

*   Flush any accumulated text output when a command exits by calling `updateOutput` before the execution promise resolves.

*   Ensure the text buffer for live display does not exceed `LIVE_OUTPUT_MAX_BUFFER_CHARS` characters.
    *   Retain only the trailing `LIVE_OUTPUT_MAX_BUFFER_CHARS` characters, discarding older content.
    *   If truncation falls on a low surrogate code unit (0xDC00–0xDFFF), advance the start by one character to avoid unpaired surrogates.

*   Forward `AnsiOutput` (non-string PTY snapshots) to `updateOutput` immediately on every arrival, bypassing throttling.

*   Implement a trailing flush on silence:
    *   If no new text chunk arrives for longer than `OUTPUT_UPDATE_INTERVAL_MS` after the last flush, flush any pending buffered text automatically via `updateOutput`.

*   Schedule the trailing flush timer for the remaining time until `OUTPUT_UPDATE_INTERVAL_MS` has elapsed since the last flush.
    *   For example, if 750 ms have passed since the last flush, set the timer for 250 ms.

*   Cancel any pending trailing flush timer when a command exits and the exit-triggered flush fires to prevent duplicate `updateOutput` calls.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.