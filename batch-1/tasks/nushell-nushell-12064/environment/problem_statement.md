## Description

Nushell's plugin system currently starts and stops a plugin process for every command invocation, which adds overhead and makes it impossible to retain plugin state between calls. We need a plugin persistence system that keeps plugin processes alive between uses, allows users to inspect and manage running plugins, and automatically stops them after a configurable idle period.

## Expected Behavior

- After a plugin command runs, the plugin process should stay running so it can be reused for subsequent calls without being re-spawned.
- Users should be able to list installed plugins, see whether each is currently running and what its process ID is.
- Users should be able to manually stop a specific plugin process.
- When a stopped plugin is needed again (e.g., to operate on a custom value it produced), the host should automatically re-spawn it.
- The system should support configurable garbage collection: a global default and per-plugin overrides for how long after a plugin goes idle before it is stopped automatically.
- Setting an invalid (negative) idle timeout should be rejected with an error.
- A plugin should be able to signal to the host that it does not want to be garbage-collected.
- The plugin should not be stopped automatically while it is still producing streaming output.
- When nushell exits, plugin processes should also exit.

## Why This Matters

This improves plugin performance by eliminating repeated process startup costs and enables plugins that maintain state across multiple invocations. The garbage collection system ensures idle plugins don't consume resources indefinitely.
