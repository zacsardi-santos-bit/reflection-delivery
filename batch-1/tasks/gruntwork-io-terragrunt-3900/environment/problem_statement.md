## Description

Terragrunt's stack configuration system currently only allows a stack file to list individual deployment units. There is no way to reference or embed entire other stacks within a stack definition. This prevents users from building modular, hierarchical infrastructure configurations where one stack composes other stacks.

Additionally, the existing validation logic for stack configuration files only knows about units — it cannot validate stack-level blocks at all, meaning any future support for nested stacks would bypass important checks for empty names, empty sources, empty paths, and duplicates.

## Expected Behavior

- A stack configuration file should support a stack block type alongside the existing unit block type, allowing users to reference other stacks as nested components.
- Each stack reference must have a unique name, a non-empty source location, and a non-empty output path.
- Validation should reject any stack reference that has an empty or whitespace-only name, source, or path, with a descriptive error identifying which stack failed and why.
- Validation should reject duplicate stack names or duplicate stack paths within the same configuration file.
- A configuration file that contains only stack references (no unit references) should be considered valid, as long as those references pass all individual field checks.

## Why This Matters

Without the ability to nest stacks, teams managing large infrastructure codebases are forced to flatten all deployment units into a single level, making configurations harder to maintain and reuse. Supporting nested stacks and validating them correctly is a foundational step toward enabling modular, scalable infrastructure organization.
