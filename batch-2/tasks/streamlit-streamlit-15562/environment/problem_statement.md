## Description

Streamlit's automatic file-watching mode fails silently or behaves unreliably when running inside the Windows Subsystem for Linux (WSL). The event-based watcher works fine for files on the native Linux filesystem, but is unreliable for files on mounted Windows drives. Since Streamlit can't easily tell which case applies for a given path, the safest approach is to always use polling-based file watching when running in WSL under the "auto" mode.

There's also no detection of WSL at all in the current codebase, so Streamlit has no way to make any WSL-specific decisions.

## Expected Behavior

- Streamlit should be able to detect whether it is running inside WSL by checking environment variables and the kernel version string.
- The detection should recognize both official WSL distributions and custom WSL2 kernels, while avoiding false positives from unrelated strings that happen to contain "wsl" as a substring.
- When the file watcher type is set to automatic and WSL is detected, Streamlit should use polling instead of event-based watching.
- The user should receive a one-time informational message explaining that polling is being used for WSL compatibility and how to override the behavior.
- If the user explicitly configures a specific watcher type (e.g., watchdog), that setting should still be honored even in WSL.

## Why This Matters

Users running Streamlit in WSL often find that file changes on their Windows drive are not picked up, leading to confusing behavior where hot-reloading doesn't work. Automatically switching to polling in WSL environments makes the default experience reliable, and giving users a clear message helps them understand what is happening and how to change it if needed.
