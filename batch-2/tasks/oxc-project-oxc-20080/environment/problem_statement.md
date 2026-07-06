## Description

The language server runs the linter on every code action request whenever no cached results are available — regardless of how the request was triggered. Code action requests are sent very frequently by editors: on file open, cursor movement, and during background scanning. This causes the linter to run constantly during normal editing, leading to significant CPU usage, memory pressure, and a sluggish user experience.

## Expected Behavior

- When a user **explicitly invokes** code actions (e.g., via a keyboard shortcut or a deliberate editor action), the server should lint the file if no cached results exist and return the resulting suggestions.
- When code actions are requested **automatically or in the background** (the default behavior for many editors), the server should only return previously cached results — without triggering a new lint run.
- If no cached results exist and the request is not an explicit invocation, the server should return an empty response rather than triggering expensive background linting.

## Why This Matters

By limiting automatic linting to explicit user-triggered requests, the language server becomes far more responsive during normal editing. Users experience less lag and reduced resource usage, while still receiving accurate code action suggestions when they actively request them.
