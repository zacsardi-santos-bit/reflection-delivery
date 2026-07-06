## Description

When a language model generates file paths using an "at-sign" reference prefix (a convention for workspace-relative file references), those paths can end up being passed directly to the file reading, writing, and editing tools. The tools currently interpret the at-sign character literally as part of the directory name, which means files get created or read from the wrong location — inside a directory whose name starts with an at-sign — instead of the intended workspace directory.

## Expected Behavior

- When a tool receives a file path that begins with the at-sign reference prefix pattern (at-sign followed by a separator, e.g., at-sign followed by a forward slash or backslash), the prefix should always be stripped and the path treated as a normal workspace-relative path.
- When a file path begins with just an at-sign (no separator), the at-sign should be stripped only if the first directory segment of the resulting path already exists in the workspace. This preserves the ability to intentionally create directories named with at-signs.
- Path resolution should guard against security issues: if a resolved path falls outside the allowed workspace boundaries, the operation should fail with a clear error message indicating the path is not within the workspace.
- Circular symbolic link chains should be detected and reported with a descriptive error rather than causing a hang or unhandled crash.
- File path correction utilities should correctly apply this same at-sign stripping logic when computing the corrected absolute path.

## Why This Matters

AI-generated file paths often carry reference prefixes that should be transparent to the underlying tools. Without defensive stripping, users end up with incorrectly named directories and files in unexpected locations, which is confusing and hard to clean up. Adding security boundaries (workspace enforcement and symlink loop detection) prevents potential abuse of path traversal.
