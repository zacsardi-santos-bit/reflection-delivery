Implement security and structural improvements to the git cloning code in the Docker builder. Prevent submodules from referencing local file paths and refactor git operations to be methods on the repository configuration.

*   Update the `gitRepo` struct in `builder/remotecontext/git/gitutils.go`:
    *   Implement a method `gitWithinDir(dir string, args ...string) ([]byte, error)`:
        *   Execute a git subprocess with the provided arguments.
        *   Use `dir` as the working directory by setting `cmd.Dir = dir`.
        *   Do not use `--work-tree` or `--git-dir` flags.
        *   Ensure it is callable on a zero-value `gitRepo{}` for general git operations.
    *   Implement a method `clone() (checkoutDir string, err error)`:
        *   Perform a full clone-and-checkout operation using `repo.remote`, `repo.ref`, and `repo.subdir`.
        *   Support cloning from HTTP remote URLs.
        *   Check out the specified `ref` and return the checkout directory path.
        *   If `subdir` is specified, scope the result to that subdirectory.
        *   Ensure submodules fetch content from HTTP URLs and are accessible under the `sub/` directory.
        *   Return a non-nil error for invalid operations, such as an invalid ref or path traversal outside the git root.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.