## Description

The GitHub CLI does not have a command to list autolink references configured for a repository. Autolinks are shorthand reference patterns that automatically convert alphanumeric identifiers (like ticket numbers) into hyperlinks pointing to external systems. Currently, the only way to view these configurations is to visit the repository's settings page in a browser.

## Expected Behavior

- Running the list command for a repository's autolinks should display all configured autolinks in a readable table when in an interactive terminal, showing each autolink's ID, key prefix, URL template, and whether it matches alphanumeric values only.
- When output is piped or redirected, the data should appear as tab-separated values with no decorative header.
- A JSON output flag should allow exporting the autolinks in structured format, with support for selecting specific fields (the ID, whether it is alphanumeric, the key prefix, and the URL template). Requesting an unrecognized field should return an appropriate error listing the valid options.
- A web flag should open the repository's autolinks settings page directly in the browser.
- When no autolinks are configured, the command should report that none were found.
- When the user lacks the required permissions (typically admin rights), the error message should clearly indicate that admin rights may be required.

## Why This Matters

Teams that rely on autolinks to integrate their GitHub repositories with external issue trackers or documentation systems currently have no quick command-line way to audit or verify these configurations. Adding a list command closes this gap, fitting naturally into existing workflows without requiring a browser.
