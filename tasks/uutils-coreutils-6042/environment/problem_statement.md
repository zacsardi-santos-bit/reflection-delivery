## Description

When using the recursive group-change command with the root protection flag enabled, the error message is misleading. Regardless of how the user specified the path — whether through a symbolic link, a path with redundant slashes, or a relative traversal sequence — the error always claims the user is operating on the literal root directory. This loses the original path context and makes it harder for users to understand what they typed that triggered the protection.

## Expected Behavior

- When the literal root directory is passed as the argument, the error message should continue to say it is dangerous to operate on that directory.
- When a path other than the literal root is given but resolves to root (via traversal sequences, extra slashes, symbolic links, etc.), the error message should show the original path the user supplied AND clarify that it is the same as the root directory.
- Relative path shorthands (current directory, parent directory) should also be blocked when the current working directory is root.
- Paths that resemble directory navigation shorthands but are not (e.g. three-dot sequences) should not be misidentified as root — they should produce a normal "not found" error.

## Why This Matters

Users relying on the root protection safety check benefit from clear, accurate error messages. When an indirect path like a symlink or a traversal sequence triggers the protection, users should see the exact path they provided so they understand why the command refused to run. This also closes gaps where certain forms of relative paths that resolve to root were not caught by the protection at all.
