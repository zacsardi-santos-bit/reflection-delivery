Implement a function to manage symbolic links for Flutter desktop plugins on Linux and Windows. Ensure these symlinks are automatically created, updated, or removed as part of the plugin management process, with an option to forcefully recreate them.

*   Implement `createPluginSymlinks` in `packages/flutter_tools/lib/src/plugins.dart` with the signature `void createPluginSymlinks(FlutterProject project, {bool force = false})`.
    *   Create symlinks in the `pluginSymlinkDirectory` for each enabled desktop platform (Linux and Windows).
    *   Name each symlink after the corresponding plugin package.
    *   Point each symlink to the plugin's package directory.

*   Implement behavior for `createPluginSymlinks`:
    *   If `force` is true, delete all existing contents in `pluginSymlinkDirectory` before recreating symlinks.
    *   If `force` is false, do nothing if all expected symlinks exist.
    *   If `force` is false and symlinks are missing, create only the missing symlinks without removing existing ones.

*   Ensure `refreshPluginsList` invokes `createPluginSymlinks` with force-like behavior to update symlinks when the plugin list changes.

*   Add a `pluginSymlinkDirectory` getter in `WindowsProject` within `packages/flutter_tools/lib/src/project.dart`:
    *   Return a `Directory` representing the location for Windows plugin symlinks.

*   Add a `pluginSymlinkDirectory` getter in `LinuxProject` within `packages/flutter_tools/lib/src/project.dart`:
    *   Return a `Directory` representing the location for Linux plugin symlinks.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.