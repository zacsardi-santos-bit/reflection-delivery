I'm working on the shell tool in our CLI and the live output display gets hammered when a command spits out a lot of text fast. Right now every single chunk we get is forwarded to the UI immediately, which is fine for short commands but for anything verbose it just floods the display with high-frequency updates and makes the live view janky.

What I want is to throttle the live text updates. The very first chunk should still show up right away so the user knows output is flowing, but after that any rapid chunks should get batched and sent at most once per update interval. When the command exits, or when it goes quiet for a full interval, whatever's buffered needs to flush immediately so nothing gets silently dropped. Oh and if the command exits and flushes right then, any pending timer-based trailing flush should be cancelled so we don't fire a duplicate.

There's also a memory thing. Some commands produce enormous output and buffering all of it is unbounded, so the live buffer should be capped at a fixed character limit and when accumulated text would blow past it we keep only the most recent characters within the limit and drop the older stuff (the full complete output still goes to the LLM response, this is just the live view). The truncation has to be Unicode-aware, so if the computed cut point lands on a low surrogate we advance the cut by one character so we don't leave a broken char at the start of the buffer.

One exception that matters: the PTY / ANSI terminal snapshots should never be throttled. Each one is already a complete snapshot of the current screen state so it needs to pass straight through every time.

Also please export that buffer size limit as a named constant so tests and other parts of the codebase can reference it directly.
