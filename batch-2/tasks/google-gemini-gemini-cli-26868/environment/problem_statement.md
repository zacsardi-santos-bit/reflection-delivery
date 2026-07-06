## Description

The text-search tool currently only works with a bundled search binary. If the bundled binary is missing or unavailable (e.g., in certain deployment environments), the feature simply fails rather than trying a system-installed version. We should add support for falling back to a system-installed binary when the bundle is not present.

At the same time, blindly trusting whatever the system PATH resolves to would be a security vulnerability—an attacker could place a malicious executable earlier in the PATH and have it picked up automatically. We need a trust-verification mechanism that only accepts a system-installed binary if it resides in a known-safe system directory (such as standard OS binary directories or common package-manager installation paths).

Additionally, certain flags accepted by the search binary can instruct it to execute arbitrary external commands, which would be dangerous. The safety classification logic for command invocations needs to understand these dangerous flags and treat them accordingly, regardless of whether the binary itself is considered trusted.

## Expected Behavior

- When no bundled binary is found, the system PATH is searched for an installed version
- A binary found via system PATH is only used if its real, symlink-resolved path comes from a trusted system directory (standard OS bin dirs, package manager dirs, etc.)
- Paths inside the current working directory are never trusted
- On Windows, trusted directories are those indicated by standard system environment variables
- On macOS/Linux, trusted directories include standard binary locations and common package manager paths
- Certain dangerous flags on the search binary invocation always classify the command as dangerous, even if the binary itself is trusted
- A bare, unqualified binary name (not an absolute path) is never considered safe, to prevent search-path hijacking
- The configuration object exposes a method to retrieve the resolved binary path, and the search tool uses this method rather than checking file existence directly

## Why This Matters

Users who don't have the bundled binary available (or who prefer their system installation) can now use the search feature without it silently failing. The security validation ensures that this flexibility doesn't open an attack surface for search-path hijacking or arbitrary command execution via dangerous flags.
