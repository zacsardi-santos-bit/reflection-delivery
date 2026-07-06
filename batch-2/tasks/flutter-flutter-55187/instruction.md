Implement improvements to the Windows desktop build pipeline by addressing dependency tracking, artifact management, and build targets. Remove unnecessary platform dependencies, create utilities for artifact handling, and ensure proper file organization and tracking.

*   Update `DepfileService`:
    *   Modify the constructor to accept only `logger` and `fileSystem` as named parameters.
    *   Remove the platform parameter from all existing calls to `DepfileService`.
    *   Implement path escaping logic based on the file system path separator style.

*   Create `unpackDesktopArtifacts` function:
    *   Location: `packages/flutter_tools/lib/src/build_system/targets/desktop.dart`.
    *   Accept named parameters: `fileSystem`, `artifactPath`, `outputDirectory`, and `artifacts`.
    *   Copy only specified artifacts from `artifactPath` to `outputDirectory`.
    *   Return a `Depfile` with inputs and outputs lists of copied files.
    *   Throw an `Exception` if any artifact in the list does not exist.
    *   Recursively copy files if an artifact is a directory, including all in the `Depfile`.

*   Update `UnpackWindows` class:
    *   Location: `packages/flutter_tools/lib/src/build_system/targets/windows.dart`.
    *   Modify `build()` to copy engine artifacts to `{PROJECT_DIR}/windows/flutter/ephemeral/`.
    *   Ensure no files are written directly to `{PROJECT_DIR}/windows/flutter/`.
    *   Generate a `windows_engine_sources.d` depfile in `environment.buildDir`.

*   Implement `DebugBundleWindowsAssets` class:
    *   Location: `packages/flutter_tools/lib/src/build_system/targets/windows.dart`.
    *   Create a build target class with a `build()` method.
    *   Copy `app.dill` from `environment.buildDir` to `flutter_assets/kernel_blob.bin`.
    *   Write a `flutter_assets.d` depfile to `environment.buildDir`.
    *   Ensure `kBuildMode` environment define is set to `'debug'`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.