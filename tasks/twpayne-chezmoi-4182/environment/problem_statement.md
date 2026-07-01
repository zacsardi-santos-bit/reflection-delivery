## Description

When running the chezmoi diagnostic command with a custom configuration file path specified via a flag, the config-file check reports an incorrect or failing status. Instead of verifying the file the user actually specified, it scans the standard system configuration directories — and when it finds a file there that doesn't match what was specified, it reports a failure or mismatch.

## Expected Behavior

- When a user specifies a non-default configuration file via a flag, the diagnostic command's config-file check should verify that specific file.
- If the specified file exists, the check should pass and report that it was found at the given path.
- This should work correctly even when a default configuration file also happens to exist in the standard location.

## Observed Behavior

If both a default config file and a custom config file exist, and the user runs the diagnostic command pointing at the custom file, the check fails with a confusing error indicating it found the wrong file.

## Why This Matters

Users who manage configurations in non-standard locations (e.g., per-environment configs, multiple profiles) rely on the diagnostic command to validate their setup. Getting a false failure from the config-file check makes it impossible to use the diagnostic command meaningfully in those workflows.
