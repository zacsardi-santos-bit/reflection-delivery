## Description

The tool that converts an Nx workspace from the legacy ESLint configuration format to the new flat config format has several correctness issues that cause the resulting configuration to behave differently than expected or to break build caching after migration.

## Issues

- **Unnecessary default ignores injected**: Every generated flat config gets a block that ignores common build output directories, even when the original config didn't ask for this. This default block should not be added automatically.

- **Extensionless config references not recognized**: When a project config uses the extends field to reference a neighboring config file without an explicit file extension (which the old format permitted), the converter doesn't recognize it as a JSON config. It falls back to a compatibility layer instead of generating a direct import, producing a less efficient config.

- **Negated ignore patterns dropped**: If an ignore patterns list combines a broad pattern (e.g., ignore an entire folder) with a targeted negation (e.g., keep one specific file), the negation gets discarded. After conversion the config ignores more files than it should.

- **Parser imports use dynamic expressions**: In ESM output, parser references are emitted as dynamic inline imports instead of static top-level imports, which can cause issues with modules that expose their API through top-level exports.

- **Stale file references in workspace and project configuration**: After conversion the old ESLint config filenames remain scattered through input lists in the workspace configuration and individual project configuration files. These stale references prevent the build cache from correctly tracking the new flat config files.

## Expected Behavior

- Converted configs should not contain any auto-injected ignore blocks that weren't present in the original.
- Extensionless base config references in the extends field should be treated as JSON configs and converted to direct flat config imports.
- Negated ignore patterns paired with broader patterns should be preserved in the output.
- ESM parser references should be converted to static top-level imports.
- All input entries in workspace and project configuration files that reference old ESLint config or ignore filenames should be rewritten to point at the new flat config filenames, with duplicates removed.

## Why This Matters

These bugs mean that a workspace migrated with the converter ends up with a subtly different linting configuration than it had before, and build caching may not work correctly because it's tracking files that no longer exist.
