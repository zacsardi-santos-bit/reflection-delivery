## Description

The OpenLineage Python client currently detects git metadata (repository URL, current commit, branch, and tag) by spawning a subprocess to call the git command-line tool. This approach is fragile: it silently fails in environments where the git binary is not installed, introduces an external process dependency, and is harder to test reliably because it requires mocking subprocess calls.

We should replace the subprocess-based git detection with a pure Python implementation that reads git's internal data files directly from the filesystem. This would make the feature work in containerized environments, sandboxed runtimes, and any context where the git CLI is unavailable.

## Expected Behavior

- The client should locate the git directory by walking up the directory tree from the working directory, without invoking any external process.
- It should support linked worktrees, where the git directory entry is a pointer file rather than a directory.
- It should read the current commit SHA, branch name, and tags by parsing git's internal file formats directly.
- It should read the repository URL from the git config file, handling edge cases like percent-encoded URLs gracefully.
- The source code location facet should be **disabled by default** — users must opt in to enable it.

## Why This Matters

Removing the subprocess dependency makes the git metadata detection more portable, reliable, and easier to test. It also avoids potential security concerns around executing external processes in restricted environments.
