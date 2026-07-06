## Description

The help text shown when invoking the tool with the help flag starts with an unnecessary article, making the description read "A cross-platform, fast and extensible general purpose fuzzy finder TUI." This leading "A" is informal and inconsistent with how most CLI tools describe themselves in their help output.

## Expected Behavior

- The help output description should begin with "Cross-platform" directly, without the leading article "A".
- The rest of the description text should remain unchanged.

## Why This Matters

A cleaner, more direct description improves the tool's presentation and consistency with conventions used by similar tools. Users seeing the help text for the first time get a more professional first impression.
