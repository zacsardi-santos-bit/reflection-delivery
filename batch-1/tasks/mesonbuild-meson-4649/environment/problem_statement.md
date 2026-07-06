## Description

Meson currently has no built-in way for projects and their subprojects to present a clean, structured summary of their build configuration at the end of the setup phase. Developers must resort to scattered log or message calls throughout their build scripts, making it hard for users to understand what was actually configured once setup completes.

## Expected Behavior

A new built-in function should be available in meson build files so that project authors can register key-value configuration information at any point during the build script. The function should support:

- Registering a single key-value pair (with or without a section name)
- Registering a dictionary of key-value pairs (with or without a section name)
- Calling the function multiple times to accumulate entries across sections
- A keyword option that renders boolean values as colored YES/NO indicators instead of raw boolean output

At the end of the configuration phase, all registered information should be printed in a formatted, aligned table. Subproject summaries should appear before the main project's summary, each prefixed with the project name and version. Keys within each section should be right-aligned based on the longest key. List values should display the first item inline with the key, and additional items indented on subsequent lines.

If a subproject fails before registering any summary information, nothing from that subproject should appear in the output.

## Why This Matters

Users running meson setup on a project—especially one with multiple subprojects—need a quick, readable overview of the final build configuration without having to parse verbose log output. A unified summary function gives project maintainers a consistent, first-class mechanism to communicate important configuration choices to end users.
