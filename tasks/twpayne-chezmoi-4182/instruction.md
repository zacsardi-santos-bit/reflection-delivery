Implement a fix for the `chezmoi doctor` command to correctly verify a custom configuration file specified via the `--config` flag. Ensure the diagnostic output accurately reflects the status of the specified file, regardless of any default configuration files present.

*   Modify the `chezmoi doctor` command to:
    *   Use the `--config` flag to specify the configuration file path.
    *   Verify the existence of the file at the specified path.
    *   Report an `ok` status with a message starting with `found` followed by the abbreviated path (using `~` for the home directory) of the specified config file if it exists.
*   Ensure the config-file diagnostic check:
    *   Does not scan standard XDG configuration directories when the `--config` flag is used.
    *   Only verifies the file path explicitly provided by the `--config` flag.
*   Guarantee that the diagnostic command outputs `ok` for the config-file check even if a default config file is present in the standard location.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.