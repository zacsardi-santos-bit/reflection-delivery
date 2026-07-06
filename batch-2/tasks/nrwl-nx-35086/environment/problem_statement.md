## Description

When Nx infers TypeScript task configurations from project tsconfig files, it records which output files each task produces. These output declarations are critical for the build cache to work correctly: if a file is not listed as a task output, the cache cannot capture or restore it.

TypeScript's build mode always generates an incremental compilation state file alongside the compiled output. This state file allows TypeScript to skip recompiling unchanged source files on subsequent runs. However, the Nx TypeScript plugin is not correctly including these state files in task output declarations:

- For typecheck targets with no output directory configured, the outputs list is completely empty — the state file is not tracked at all.
- For build targets where both an output directory and a source root directory are configured, the outputs list uses an overly broad wildcard pattern that may not match the actual location of the state file.

## Expected Behavior

- Every TypeScript task (typecheck and build) must include the precise path to the incremental compilation state file in its outputs list.
- The state file path should be an exact, specific path — not a wildcard or glob pattern.
- The path should be determined from the tsconfig filename: the state file lives in the output directory (or at the project root if no output directory is set), with a filename derived from the tsconfig configuration filename — specifically, the base name of the config file with its JSON extension replaced by the build state file extension.
- When an explicit override for the state file location is set in the tsconfig, that path should be respected.
- When a project contains multiple internal TypeScript configuration references, each one's state file must be tracked separately in the outputs list.
- Paths that fall within the project directory should use a project-relative token; paths that fall outside (e.g., in a workspace-level output folder) should use a workspace-relative token.

## Why This Matters

Without accurate output declarations, the Nx build cache cannot capture and restore TypeScript incremental compilation state. After a cache hit, TypeScript loses its incremental compilation advantage and must recompile from scratch, degrading build performance unnecessarily.
