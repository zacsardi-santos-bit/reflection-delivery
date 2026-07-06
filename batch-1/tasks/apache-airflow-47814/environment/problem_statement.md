## Description

The Airflow project needs a utility for publishing documentation packages to cloud object storage as part of the release process. Currently there is no automated way to discover which documentation packages are available locally, filter out ones that should not be published, or handle the distinction between versioned and bulk publishing modes.

## Expected Behavior

- The tool should be able to list all available documentation packages in a given local archive directory, and exit with an error if that directory doesn't exist.
- Users should be able to specify a comma-separated list of documentation package names (or partial names) to exclude from publishing. Dot characters in exclusion patterns should match hyphen separators in package names.
- If all packages are excluded and nothing is eligible to publish, the tool should exit with an error.
- When publishing versioned stable docs, each package should be uploaded to both a version-specific path and a "stable" alias path at the destination.
- When publishing all docs (non-versioned), each package should be uploaded to its corresponding path at the destination.
- An overwrite flag should control whether packages that already exist at the destination are re-published. If overwrite is disabled and a package already exists at the destination, that package must be skipped.
- The tool must track which source-to-destination path pairs were actually processed.

## Why This Matters

This utility enables the Airflow release team to automate and reliably manage documentation deploys to cloud storage, with control over what gets published, support for stable aliasing, and safety against accidental overwrites.
