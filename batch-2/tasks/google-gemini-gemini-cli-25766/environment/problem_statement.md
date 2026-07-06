## Description

When working in plan mode, file paths for plan-related operations are currently being handled inconsistently. Rather than treating plan file paths as relative to the designated plans directory, the system requires paths to be specified relative to the workspace root. This makes the feature harder to use correctly and creates confusion when subdirectories are involved.

Additionally, the AI-facing system prompt in plan mode displays the absolute path to the plans directory on the user's filesystem, rather than a meaningful relative path from the project root. This is noisy and potentially sensitive information that should instead be presented as a human-friendly relative path.

## Expected Behavior

- Plan file paths should be treated as relative to the plans directory itself (just a filename or a nested subdirectory path within plans), rather than being relative to the workspace root.
- Nested subdirectory paths within the plans directory should work correctly, with any required parent directories being created automatically.
- The system prompt shown to the AI in plan mode should display the relative path from the project root to the plans directory, not the absolute filesystem path.
- Path security validation (preventing traversal outside the plans directory) should correctly handle these relative paths and report them using the relative path in any error messages.
- The application configuration should provide a way to retrieve the project root directory so that relative paths can be computed throughout the codebase.

## Why This Matters

Using absolute paths in the AI-facing prompt exposes internal filesystem structure unnecessarily. Requiring workspace-root-relative paths for plan files creates confusion and bugs when users or the AI specify filenames in a natural way (without the plans directory prefix). Making the path handling consistent and relative-path-based makes plan mode more robust and user-friendly.
