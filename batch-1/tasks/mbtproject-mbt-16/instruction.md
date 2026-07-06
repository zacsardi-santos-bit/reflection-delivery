Implement a feature in the monorepo build tool to support building based on the current local working directory state. This should allow developers to build modules with uncommitted changes or newly added modules without committing them first. Ensure the tool can create a manifest from the local directory and execute builds using this manifest.

*   Implement the `getDiffFromIndex` function in `lib/git_utils.go`.
    *   Accept a `git.Repository` object as a parameter.
    *   Return a `git.Diff` object representing uncommitted changes.
    *   Ensure the returned diff supports a `NumDeltas()` method that returns the count of modified files.

*   Implement the `ManifestByLocalDir` function in `lib/manifest.go`.
    *   Accept an absolute directory path and a boolean `all` flag as parameters.
    *   Return a `Manifest` with the `Sha` field set to "local" and the `Dir` field set to the provided path.
    *   When `all=false`, include only modules with uncommitted changes or new untracked modules.
        *   If no modules have changes, return an empty `Modules` list.
        *   Include modified modules and new modules with `name` set to the directory name and `hash` set to "local".
    *   When `all=true`, include all known modules in the `Modules` list.

*   Implement the `BuildDir` function in `lib/build.go`.
    *   Accept a `Manifest`, stdin/stdout/stderr I/O writers, and a build-stage callback function.
    *   Execute each module's build script, writing output to the provided stdout writer.
    *   Invoke the callback with `BuildStageBeforeBuild` before each module's build and `BuildStageAfterBuild` after completion.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.