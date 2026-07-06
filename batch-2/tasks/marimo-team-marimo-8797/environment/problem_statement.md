## Description

When Python's built-in mechanism for redirecting bytecode cache files to a separate directory is active, marimo ignores this setting and continues placing its output artifacts — session caches, generated images, and other notebook outputs — alongside the notebook source files. This causes failures in environments where the notebook's source directory is read-only, or where keeping source and output files separate is a hard requirement (e.g., certain containerized or shared-filesystem setups).

## Expected Behavior

- When the system-level Python cache redirection is active and the notebook has an absolute path, all marimo output artifacts (session caches, generated images) should be written under the designated cache directory, mirroring the notebook's directory tree.
- When the cache redirection is not active, behavior should remain unchanged.
- Relative notebook paths and the case where no notebook path is provided should not be affected by the cache redirection setting.
- The output directory path should be resolved lazily (only when first needed), rather than eagerly at initialization, to avoid errors during startup.

## Why This Matters

Developers running marimo in environments with read-only source directories or strict separation between source and output files are blocked because marimo unconditionally writes outputs next to the notebook. Supporting the system-level cache redirection setting removes this obstacle and aligns marimo's behavior with standard Python tooling conventions.
