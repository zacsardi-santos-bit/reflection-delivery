## Description

The kinflate tool needs better support for managing configmap entries in manifest files and for reading and writing those manifest files in a testable way. Currently, there is no safe way to look up an existing configmap by name and update it — the only option is to append a new entry, which can lead to duplicates. There is also no way to incrementally add data sources (literal key-value pairs, files, and environment files) to a configmap without overwriting prior data, and no guard against accidentally setting an environment file source more than once per configmap.

Additionally, reading and writing manifest files is done with direct filesystem calls, making unit testing difficult. There is no reusable abstraction that supports a virtual or in-memory filesystem.

## Expected Behavior

- Looking up a configmap by name in a manifest should return the existing entry if one exists, or create and append a new one if it does not — never creating duplicates.
- Merging data sources into a configmap should accumulate literal sources and file sources across multiple operations rather than overwriting them.
- Attempting to set an environment file source on a configmap that already has one should be rejected with an error.
- Manifest files should be readable and writable through a loader abstraction that accepts a pluggable filesystem, enabling in-memory testing without touching the real disk.
- Writing to an empty filename should produce an error.
- A written manifest should be recoverable by reading it back, with data integrity preserved.

## Why This Matters

These changes make the configmap management commands safer and more composable, and they make the manifest I/O layer testable without requiring a real filesystem. This is foundational for reliably building and testing kinflate workflows.
