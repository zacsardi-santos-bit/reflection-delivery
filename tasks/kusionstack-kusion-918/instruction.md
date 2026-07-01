Implement a function to package local files or directories into a gzip-compressed tar archive for use as OCI artifacts. Ensure the function handles different path formats and applies ignore patterns similar to gitignore rules.

*   Implement the `build` function in `pkg/oci/client/build.go` with the signature:
    *   `build(artifactPath, sourceDir string, ignorePaths []string) (err error)`
    *   Ensure it packages the contents of `sourceDir` into a gzip-compressed tar archive at `artifactPath`.
    *   Accept both file and directory paths for `sourceDir`.
    *   Return a non-nil error if `sourceDir` does not exist.

*   Handle various path formats:
    *   Support relative, absolute, and leading-dot-slash paths (e.g., './dir').

*   Implement ignore pattern functionality:
    *   Exclude files and directories matching gitignore-style patterns in `ignorePaths`.
    *   Support negation patterns (prefixed with '!') to override exclusion rules.

*   Ensure the function creates a valid archive:
    *   When given a directory, include all contents unless excluded by ignore patterns.
    *   When given a file, include only that file at the archive root.

*   Prepare test data under `pkg/oci/client/testdata/artifact`:
    *   Include `deployment.yaml` as a source artifact.
    *   Include `ignore.txt` matching an ignore pattern.
    *   Include `ignore-dir/` with at least one file, matching an ignore-directory pattern.
    *   Include `somedir/git/` with at least one file, matched by the pattern "somedir/git".
    *   Include `somedir/` as a parent directory with other files not matched by ignore patterns.
    *   Include `deploy/` with at least one file, which should be present in the archive when "!/deploy" is in the ignore list.

*   Provide a public wrapper method in the `Client` struct:
    *   `func (c *Client) Build(artifactPath, sourceDir string, ignorePaths []string) error`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.