Improve the error handling and robustness of Flutter's build tools by implementing specific changes to handle file-not-found errors, transient file locking on Windows, and symlink creation issues on Apple platforms. Ensure that error messages are clear and actionable, and that operations are resilient to common filesystem issues.

*   Implement error handling in the `ErrorHandlingFileSystem` class:
    *   Convert OS error codes 2 and 3 on all platforms to a `ToolExit` with the message 'The file or directory could not be found'.
    *   Ensure both synchronous and asynchronous file operations handle permission-denied errors (OS error 13) by converting them into `ToolExit` exceptions.

*   Enhance Windows file write operations:
    *   Automatically retry operations encountering OS error code 32 (sharing violation) using the `overrideWindowsRetryBackoffs` variable for retry scheduling.
    *   If retries are exhausted, throw a `ToolExit` with the message 'The file is being used by another program'.

*   Update the `deleteIfExists` static method:
    *   Use a type-agnostic check (follow-links: false) to correctly detect and delete broken symlinks.
    *   Return false if the path does not exist (`FileSystemEntityType.notFound`).
    *   Ensure the method deletes the correctly-typed entity if the on-disk type differs from the entity reference.

*   Improve Swift Package Manager symlink creation in the `SwiftPackageManager` class:
    *   Before creating a symlink, delete any existing directory, file, or broken symlink at the target path.
    *   Handle OS error 17 ('File exists') by verifying if the existing symlink points to the correct target and treating the operation as successful if it does.

*   Use the `overrideWindowsRetryBackoffs` variable:
    *   Located in `packages/flutter_tools/lib/src/base/error_handling_io.dart`.
    *   Control retry behavior for Windows file locking issues with a list of `Duration` values or use the default schedule if null.

*   Ensure the `generatePluginsSwiftPackage` method in `SwiftPackageManager`:
    *   Manages pre-existing filesystem entities at symlink paths without following symlinks.
    *   Deletes existing entities before creating new symlinks.
    *   Treats symlink creation as successful if an existing symlink points to the correct target.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.