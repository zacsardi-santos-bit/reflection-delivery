## Description

When working in a monorepo or a project where the workspace folder is nested inside a parent repository, the AI coding assistant currently ignores customization files (such as coding guidelines or agent instructions) that live in ancestor directories above the workspace root. This means teams that organize their conventions in a top-level parent repository cannot take advantage of those shared guidelines — even when they're explicitly relevant to the nested project.

## Expected Behavior

- A new configuration option should allow users to enable or disable searching parent directories above the workspace root for customization and instruction files.
- When the option is enabled, the assistant should pick up instruction files (such as Claude configuration files, Copilot instruction files, and agent guidelines) from parent folders, not just from within the workspace root and home directories.
- When the option is disabled (the default), behavior should remain unchanged — no parent-directory search occurs.

Additionally, when a hook definition file exists in the workspace but the workspace is not trusted, the discovery diagnostics for hooks should explicitly report those files as skipped rather than silently ignoring them. The reason for skipping should clearly indicate that the workspace is not trusted, so users can diagnose why their hooks are not active.

Finally, built-in internal customizations should no longer be exposed through the listings for agent skills, prompt slash commands, and prompt file lists. These internal items were previously mixed in with user-defined ones, which could cause confusion and incorrect result counts.

## Why This Matters

Developers working in monorepos or multi-project repositories need a reliable way to share AI assistant configurations across nested projects. Without parent-folder discovery, these shared files are invisible to the assistant. The diagnostics improvement helps users quickly understand workspace trust issues, and cleaning up the internal-items exposure makes the public API surface more predictable.
