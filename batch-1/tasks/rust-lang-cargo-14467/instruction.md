Implement the necessary changes to ensure that Cargo commands correctly handle namespaced package names containing double-colon separators. Update the package specification parser to correctly interpret these names and ensure compatibility with standard commands.

*   Modify the package ID specification parser:
    *   Ensure it distinguishes between '::' used as a namespace separator in package names and ':' or '@' used as separators before version numbers.
    *   Prevent incorrect splitting of namespaced names like 'foo::bar' into a name 'foo' and a malformed version 'bar'.

*   Update the 'cargo pkgid' command:
    *   Ensure it outputs the package identifier in the format 'path+[URL]#name@version', correctly preserving '::' in the package name.

*   Update the 'cargo update' command:
    *   Allow it to accept a namespaced package name (e.g., 'foo::bar') as a valid package specification argument without error.
    *   Ensure it accepts a full package ID specification containing a namespaced name in the fragment (e.g., 'path+URL#foo::bar@0.0.1') as a valid package specification argument without error.
    *   When no update is needed, ensure it produces no stdout output and emits '[LOCKING] 0 packages to latest compatible versions' on stderr.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.