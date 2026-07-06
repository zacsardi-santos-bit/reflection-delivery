## Description

The CLI currently uses a dot-based naming scheme to differentiate commands that come from different sources when conflicts arise, but there is no standard way to identify where a command originated. Additionally, extension commands automatically have their source name prepended to their description in brackets, which is redundant and clutters the command list.

## Expected Behavior

- Every command loaded from a file should carry metadata indicating its source: user configuration, project/workspace configuration, or a specific extension.
- User commands should be labeled under a "user" namespace.
- Project-level commands should be labeled under a "workspace" namespace.
- Extension commands should be labeled under the extension's own name as a namespace.
- When displaying or resolving command names, the namespace and command name should be combined using a colon separator (e.g., a namespace prefix, a colon, and the command name).
- When a namespaced command name conflicts with an existing command, the system should append an incrementing numeric suffix to find a unique name.
- Conflict reporting should use the fully namespaced name, not just the short original name.
- Extension command descriptions should be plain text as defined in the command file — no bracket-prefixed extension name should be automatically added.

## Why This Matters

This makes the command system clearer and more consistent: users can immediately see which source a command comes from, conflict resolution uses a predictable and readable colon-separated format, and descriptions stay clean without redundant auto-generated prefixes.
