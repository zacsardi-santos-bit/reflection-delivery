## Description

Rust-analyzer already supports several import organization styles — grouping by crate, grouping by module, or one per item. However, there is no support for a style where all imports are collected into a single top-level braces block, i.e. a pattern where the braces appear at the very top level of the use statement rather than after a path prefix. Some codebases or developers prefer this style to keep all imports visually uniform and grouped together.

## Expected Behavior

- A new import granularity setting should be introduced for the "one block" style, where imports are merged into a single top-level braced use statement.
- When this setting is active, inserting a new import into a file should wrap it in the appropriate braced form and merge it with any existing imports that have the same visibility and attributes.
- If an existing import uses a different visibility or has different attributes, the new import should be inserted as its own separate top-level braced statement rather than merged.
- The tool's automatic granularity detection should recognize this style from existing code, returning the appropriate guess when a file contains imports in this format.
- The detection logic should treat multiple same-style blocks with the same visibility (or same attributes) as an inconsistent/unknown style, since the one-block style implies everything would be in a single group.

## Why This Matters

Without this support, users who prefer the single top-level braces style would see the tool insert new imports in a different format, breaking their preferred convention. Adding detection and insertion support allows the tool to correctly round-trip this style, preserving the developer's formatting intent automatically.
