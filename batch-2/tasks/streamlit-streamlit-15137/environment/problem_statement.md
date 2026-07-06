## Description

Streamlit's ASGI server startup always configures its websocket handling with a legacy protocol setting, even when running on a newer version of the underlying server library that offers an improved implementation. The newer implementation, available from a specific release of the server library onward, provides a cleaner separation between I/O and protocol logic and adds full support for ping interval and timeout settings. By always falling back to the older approach, Streamlit users miss out on these improvements automatically.

## Expected Behavior

- When the installed server library is below a certain version threshold, websocket handling should continue using the existing legacy protocol (as it does today).
- When the installed server library is at or above that threshold (specifically the first stable release to introduce the new implementation), the improved websocket protocol should be selected automatically.
- Pre-release versions of the threshold release (release candidates, dev builds) should be treated as below the threshold and use the legacy protocol.
- All other configuration values (SSL certificates, ping intervals, compression settings, logging flags) should be passed through unchanged.

## Why This Matters

Users running newer installations currently don't benefit from the improved websocket stack. Making the selection automatic means Streamlit transparently takes advantage of a better implementation when it's available, without requiring any user configuration changes, and falls back gracefully for older installations.
