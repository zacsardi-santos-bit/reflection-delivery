I'm refactoring our sandbox system and hitting a design wall. Right now security policies for sandboxed command execution, stuff like which file paths are accessible and whether network's allowed, get passed into the sandbox manager's constructor, which means every command a manager runs is stuck with the same policy. I want to move these per-execution settings (allowed file paths, network access, env var sanitization rules) out of the constructor and into the individual command requests so one manager instance can serve multiple commands with different policies. This hits all three platform managers, Linux, macOS, and Windows.

While I'm in here I also want a shared helper for validating and deduplicating path lists. It should reject any path that isn't absolute with a clear error naming which path failed, and silently drop duplicate entries while preserving order.

On Linux, allowed paths beyond the primary workspace should use a soft bind variant that doesn't fail at mount time if the path doesn't exist yet, oh and the manager should dedupe the workspace against the allowed paths after normalizing trailing slashes.

On macOS, move the sandbox profile building logic inside the manager itself instead of a separate helper module, and resolve symlinks in workspace and allowed path entries to their real paths to prevent escapes. If a path doesn't exist on disk, walk up to the nearest existing parent, resolve that, then reconstruct the full path. If resolution fails for any other reason (like a permission error), let it propagate.

On Windows, the Low Integrity access grant should target the workspace configured at construction time, not the per-request working directory, plus any additional paths from the per-request policy.

The point is reuse: same manager, different access policies per command, plus centralized path validation so misconfigured paths get caught early with actionable messages instead of slipping through.
