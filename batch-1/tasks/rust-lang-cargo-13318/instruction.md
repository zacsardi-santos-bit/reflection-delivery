Ensure that when a git dependency is declared with a branch specifier, the package ID is correctly formatted and recognized by cargo. Implement changes to cargo's package selection logic to correctly handle package IDs containing URL query strings, preventing them from being misinterpreted as glob patterns.

*   Modify the package ID output:
    *   Ensure `cargo pkgid` outputs a package ID in the format `git+<url>?branch=<name>#<version>` for git dependencies with branch specifiers.
*   Update package selection logic:
    *   Ensure that when a package ID containing a `?` character is passed to `cargo build -p`, it is recognized as a valid package specifier.
    *   Prevent cargo from interpreting `?` in package IDs as a glob wildcard.
    *   Implement a check to determine if an argument is a valid package ID spec before treating it as a glob pattern.
*   Validate successful build:
    *   Ensure that using `cargo build -p <pkgid>` with a git-dependency package ID that includes a query string results in the dependency being compiled successfully.
    *   Verify no errors about unmatched packages occur during this process.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.