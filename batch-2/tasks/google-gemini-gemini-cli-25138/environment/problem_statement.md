## Description

When using plan mode in the CLI, the AI is currently expected to reference plan files using paths relative to the project root (e.g., "plans/myplan.md"). This is unnecessarily coupled to the project's directory layout and can cause confusion. Additionally, the system prompt tells the AI to write plan files to an absolute filesystem path, which is fragile, machine-specific, and exposes internal directory structure to the AI.

## Expected Behavior

- Plan file references should use simple relative filenames within the plans directory (e.g., "myplan.md" or "subfolder/myplan.md") rather than paths relative to the project root.
- The system prompt should show the plans directory as a path relative to the project root, not as an absolute path.
- Path validation for plan files should correctly handle nested subdirectories within the plans folder.
- When writing or editing plan files, a relative path like "conductor/tracks/plan.md" should automatically be resolved within the plans directory, with intermediate directories created as needed.
- The configuration interface should expose a way to retrieve the project root directory.

## Why This Matters

Using absolute paths in the system prompt is brittle across different machines and environments. Simplifying how the AI references plan files reduces errors and makes plan mode more predictable. Supporting nested plan subdirectories also gives users more flexibility in organizing complex plans.
