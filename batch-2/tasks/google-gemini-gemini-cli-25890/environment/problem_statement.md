## Description

The CLI tool is supposed to warn users when they launch it from their home directory, since doing so can expose sensitive files to AI-assisted tools. However, this warning has two bugs that prevent it from working correctly in common scenarios.

## Problem 1: Symlinked Home Directories

On many systems, the home directory path is actually a symbolic link to the real directory. When this is the case, the current path comparison doesn't resolve the link before comparing, so the warning is never shown — even when the user is running directly from their home directory. The comparison should resolve both paths to their real locations before checking if they match.

## Problem 2: Application Home Overrides Environment Variable

Some environments allow users to configure a custom application home directory via an environment variable. When this custom home is set to the user's current working directory, the warning incorrectly fires — even though the user is not working in their actual OS home directory. The home directory warning check should use the operating system's home directory directly, not any application-level override.

## Expected Behavior

- The home directory warning should fire when the current working directory is the actual OS home directory (whether or not it's a symlink).
- The home directory warning should fire when the current working directory resolves (via symlinks) to the same location as the OS home directory.
- The home directory warning should NOT fire when the current working directory is merely a subdirectory of the home directory.
- The home directory warning should NOT fire when an application-level home environment variable is set to a path other than the OS home, and the user is working in that configured path.

## Why This Matters

Users relying on this safety warning may not receive it due to these bugs, leaving them unaware that the CLI is operating in a sensitive location. Conversely, users with legitimate custom home configurations may see spurious warnings that don't apply to their situation.
