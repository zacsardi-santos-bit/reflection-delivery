# Refactor: Move per-execution sandbox policies from constructor to per-request

## Description

Our sandbox managers (for Linux, macOS, and Windows) currently accept security policy settings — such as which file paths should be accessible and whether network access is permitted — as constructor arguments. This design means every command executed by a single manager instance must use the same fixed security policy. There is no way to apply different access controls for different command executions without creating an entirely new manager instance each time.

We also lack a centralized, shared utility for validating and deduplicating the list of paths provided as sandbox-accessible locations. As a result, duplicate or relative paths may silently slip through without a clear error.

## Expected Behavior

- Per-execution security settings (allowed file paths, network access, environment variable sanitization rules) should be moved out of the manager constructor and into individual command requests, so a single manager instance can serve multiple commands with different policies.
- A new shared utility should validate and deduplicate path lists: it should reject any non-absolute path with a descriptive error message, and silently remove duplicate entries preserving order.
- On Linux, allowed paths beyond the workspace should use a "soft bind" that does not fail if the path does not exist at mount time.
- On macOS, the sandbox profile should be generated from per-request policy; symlinks should be resolved to prevent path-based escapes; if a path does not exist, the manager should resolve the nearest existing parent directory and reconstruct the path from there; permission errors during path resolution should be re-thrown.
- On Windows, Low Integrity access should be granted to the configured workspace (set at construction time) and all per-request allowed paths.

## Why This Matters

This change enables the same sandbox manager instance to be reused across multiple command executions with different access policies. It also centralizes path validation, reducing duplication across platform-specific implementations and making it easier to catch misconfigured paths early with actionable error messages.
