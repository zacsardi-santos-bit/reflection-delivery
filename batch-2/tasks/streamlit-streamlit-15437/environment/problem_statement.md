## Description

When Streamlit starts in interactive (non-headless) mode, it should nudge users who haven't yet installed the official agent skills to do so. These skills enable AI coding assistants to build and debug Streamlit apps more effectively. Currently, there is no such reminder — users may not know the skills exist or how to install them.

## Expected Behavior

- When a Streamlit server starts in interactive mode and the agent skills are **not** already installed, a short recommendation message should be printed to the terminal. The message should tell users that installing the official Streamlit skills will help agents write better apps, and should reference the streamlit skills command for installation. Specifically, the output should include:
  - "Help agents write better Streamlit apps?"
  - "streamlit skills"
  - "Install the official Streamlit skills"
- If the skills **are** already installed (in any project-local or global agent directory), no message should appear.
- In **headless mode** (e.g., cloud deployments, CI pipelines), the message should never appear.
- When the terminal welcome message is suppressed via configuration, the message should not appear and the installation check should be skipped entirely.

## Installation Detection

A new public function is needed to detect whether the bundled skill is already present on the system:

- It should check both project-local and global agent skills directories.
- It should return true if the skill is found (as a directory or symlink) in **any** of those locations.
- It should handle filesystem errors gracefully: if resolving one category of directories fails, it should still check the others.
- If all directory lookups fail, it should return false rather than raising.

## Why This Matters

AI coding assistants that work with Streamlit can use these skills to produce higher-quality code. Without a prompt at startup, many developers may never discover the skills are available.
