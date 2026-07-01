Implement the following improvements to the go-task/task codebase by separating file search from directory resolution and enhancing configuration loading and merging.

*   Update the `Search` function in `internal/fsext/fs.go`:
    *   Modify it to return only the resolved absolute path to the found file and an error.
    *   Remove any logic related to returning a directory.

*   Add a new `ResolveDir` function in `internal/fsext/fs.go`:
    *   Signature: `ResolveDir(entrypoint, resolvedEntrypoint, dir string) (string, error)`
    *   If both `entrypoint` and `dir` are non-empty, return the absolute path of `dir`.
    *   If either is empty, return the parent directory of `resolvedEntrypoint`.
    *   Ensure it returns no error under normal operation.

*   Implement a new `GetConfig` function in `taskrc/taskrc.go`:
    *   Signature: `GetConfig(dir string) (*ast.TaskRC, error)`
    *   Return `nil` with no error when no configuration files are found.
    *   Read configuration from `$XDG_CONFIG_HOME/task/` if the `XDG_CONFIG_HOME` environment variable is set, looking for `.taskrc.yml` or `.taskrc.yaml`.
    *   Walk up the directory tree from `dir`, searching for `.taskrc.yml` or `.taskrc.yaml` files in each directory until reaching the root.
    *   Merge all discovered configuration files so that local project directory configurations have the highest priority, followed by ancestor directory configurations, and finally the XDG config directory configurations.
    *   Ensure that for the `Experiments` map, keys set in more local configurations override those from more global ones, while keys unique to lower-priority configurations are preserved.

*   Update the `TaskRC` struct in `taskrc/ast/taskrc.go`:
    *   Add a `Version` field of type `*semver.Version` with the YAML tag `version`.
    *   Add an `Experiments` field of type `map[string]int` with the YAML tag `experiments`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.