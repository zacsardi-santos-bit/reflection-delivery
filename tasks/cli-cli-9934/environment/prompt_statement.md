I'm working on adding update notification support for extensions in the GitHub CLI. Right now, the tool can notify users when a new version of the CLI itself is available, but there's no equivalent mechanism for installed extensions. I'd like to add a function that checks whether an extension has a newer release available, with rate-limiting so it doesn't check more than once every 24 hours per extension. Local extensions (those installed from a local path) should be skipped entirely since they don't have remote releases.

I also need the system to clean up the cached update state whenever an extension is removed or reinstalled — otherwise stale notifications could appear after a reinstall.

Users should be able to opt out of extension update notifications using their own environment variable, separate from the one that disables the main CLI's own update notifications. Like the existing update check behavior, it should also be automatically suppressed in CI environments and codespace environments.

The function that constructs the extension command should be updated to accept a pluggable check function so the update-checking behavior can be injected rather than hardcoded. Extension names shown in upgrade output should not be padded to a fixed width.
