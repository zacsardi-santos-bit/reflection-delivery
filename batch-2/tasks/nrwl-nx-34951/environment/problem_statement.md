## Description

The nx CLI currently has no built-in tab completion support for interactive shells. When working in a large monorepo with dozens of projects, targets, and generator plugins, developers must remember all names from memory — pressing Tab provides no hints. This is particularly painful for flags like projects or focus where the valid values are workspace-specific.

## Expected Behavior

- Pressing Tab after the run command should suggest project names with a colon suffix, then complete to project:target combinations at the second stage
- Pressing Tab after the generate command should suggest both plugin names (with colon) and bare generator names from across all installed plugins — and filter by what's been typed so far
- Pressing Tab for flag values like projects, focus, exclude, target, and their short aliases should suggest valid workspace project or target names
- The show project, show target, show target inputs, and show target outputs subcommands should each complete their positional argument appropriately
- The add command should suggest available plugins filtered by prefix
- Typing a target name directly (e.g. typing a build command) should complete project names that have that target
- The system should work for bash, zsh, fish, and PowerShell with installable wrapper scripts
- The wrapper scripts should walk up the directory tree to find a workspace-local nx binary and gate verbose output on an environment variable
- The zsh script in particular must handle colon-containing values (like project:target formatted values) correctly without splitting them

## Why This Matters

Without shell completion, exploring an unfamiliar Nx workspace is tedious. Completion lets developers discover available projects, targets, and generators interactively without leaving the terminal or consulting documentation. The system should be robust to missing workspace data (returning empty suggestions rather than crashing) and work correctly for workspace-local plugins in addition to installed node_modules packages.
