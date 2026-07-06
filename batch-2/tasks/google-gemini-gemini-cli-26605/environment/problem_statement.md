## Description

Memory file paths are being displayed with incorrect casing on macOS and Windows. When memory context files are discovered and shown to users (e.g., in memory listings or memory show output), filenames like "MEMORY.md" appear lowercased as "memory.md". This happens because the path normalization utility used during memory discovery converts paths to lowercase on case-insensitive filesystems, even though the original on-disk casing should be preserved for display purposes.

Additionally, the memory management command currently has a fixed set of subcommands regardless of the active feature configuration. When an automatic memory management mode is enabled, the manual "add" subcommand should be hidden since it is no longer applicable. This requires the command to be constructed dynamically based on runtime configuration rather than as a static object.

There is also a reliability issue in the memory extraction pipeline: when the AI extraction agent produces a patch file with a mismatched hunk line count (more lines than the diff header declares), the malformed patch is currently recorded and surfaced to the user. Instead, malformed patches should be silently deleted before any recording or notification occurs.

## Expected Behavior

- Memory file paths shown to users preserve their original on-disk casing (e.g., "MEMORY.md" stays "MEMORY.md", not "memory.md")
- A new project memory index file (distinct from the legacy context filename) is recognized and preferred as the primary memory path for a project
- The memory management command's available subcommands reflect the active configuration at runtime
- When automatic memory mode is on, the manual add subcommand is hidden
- Malformed AI-generated memory patches are discarded silently without any user notification or state recording

## Why This Matters

Users seeing incorrect filename casing in memory listings is confusing and breaks the expected display of memory files. The feature flag behavior for subcommand visibility is needed to avoid exposing irrelevant options when automatic memory management is active. Silent discard of malformed patches prevents noise from appearing in the memory management workflow when the extraction agent produces invalid output.
