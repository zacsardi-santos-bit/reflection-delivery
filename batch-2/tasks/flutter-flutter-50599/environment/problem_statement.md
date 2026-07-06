## Description

Flutter's desktop plugin support for Linux and Windows is missing a mechanism to make installed plugins discoverable on the filesystem via symbolic links. The build system for these desktop targets needs to locate plugin packages through a predictable directory structure, but right now there is no way to create or manage those symlinks as part of normal plugin management.

## Expected Behavior

- When a project's plugin list is refreshed and Linux or Windows desktop support is enabled, symlinks pointing to each plugin package should be automatically created inside a dedicated directory within the platform project.
- Symlinks should be named after their corresponding plugin package.
- There should be a way to force a full recreation of all symlinks, which clears out any stale entries before creating fresh ones.
- When symlinks are recreated without the force option, existing content should not be disturbed — only missing symlinks should be added (repair behavior).
- When the plugin list is refreshed and plugins are removed or changed, the old symlinks should be cleaned up automatically.

## Why This Matters

Without these symlinks, the Linux and Windows desktop build systems cannot reliably locate plugin packages, which breaks the plugin pipeline for desktop targets. Having this managed automatically as part of plugin list refresh means developers do not need to manually maintain these links.
