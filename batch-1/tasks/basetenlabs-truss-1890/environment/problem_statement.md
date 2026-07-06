## Description

The training CLI needs two improvements:

1. **Training initialization from examples**: There is currently no way for a user to discover available training example projects from the hosted repository or download one to bootstrap their local workspace. We need to add the ability to list available examples (fetching from the remote catalog) and download a chosen example's files to a local directory. The download logic needs to handle nested directories recursively, and should have special behavior for a directory named "training" — its contents should land directly in the destination directory rather than a "training" subdirectory.

2. **Clearer checkpoint selection prompt**: The prompt shown to users when interactively selecting checkpoints to deploy is ambiguous. The current wording doesn't tell users what to do after making their selections. The message should be updated to explicitly tell users to press Enter when they are done selecting.

## Expected Behavior

- A function to retrieve available training example names from the remote catalog, returning only directories (not files), with optional authentication token support and configurable repository/subdirectory parameters.
- A function to retrieve the contents listing for a specific example, supporting the same authentication and configuration options. A missing example (404) should return an empty result silently; other errors should report a message.
- A function to recursively download a GitHub API directory to a local path, with proper error handling — returning a success or failure indicator.
- The interactive multi-select prompt for checkpoint deployment must display a message that guides users on how to select items AND how to confirm their selection.

## Error Handling

- On network or HTTP errors when fetching example listings or example info (except 404), the tools should output a descriptive error message directing the user to file a GitHub issue.
- The download function should catch all errors and return a failure value, printing a message about what went wrong.

## Why This Matters

Users need a quick way to scaffold a training project from a curated set of examples without manually downloading files from GitHub. The checkpoint selection UX improvement reduces confusion for users who don't know how to complete the multi-select interaction.
