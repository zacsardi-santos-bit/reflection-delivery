Implement the infrastructure apply subcommand in the developer CLI tool to apply configuration changes to a specified environment (biome). Ensure the command checks for and performs any necessary initialization steps automatically before applying changes.

*   Implement `findBiomeDir(moduleRoot, name string) string` in `internal/cmd/gocdk/main.go`:
    *   Return the path to the named biome directory using `filepath.Join(moduleRoot, "biomes", name)`.

*   Update `processContext` struct in `internal/cmd/gocdk/main.go`:
    *   Include a `stdin io.Reader` field alongside existing `workdir`, `stdout`, and `stderr` fields.

*   Implement the `apply` function in `internal/cmd/gocdk/apply.go`:
    *   Accept `context`, `*processContext`, and `args []string`.
    *   Extract the biome name from `args` as the sole positional argument.
    *   Determine the module root using `pctx.workdir`.
    *   Compute the biome directory using `findBiomeDir`.
    *   Call `ensureTerraformInit` to check initialization.
    *   Execute 'terraform apply' in the biome directory with `pctx.stdin/stdout/stderr` wired to the subprocess.

*   Implement `ensureTerraformInit` in `internal/cmd/gocdk/apply.go`:
    *   Check for a '.terraform' directory inside `biomePath`.
    *   If the directory does not exist, run 'terraform init' in `biomePath` using `pctx.stdout/stderr`.
    *   If the directory exists, return nil without further action.

*   Update the `run` function to dispatch the 'apply' subcommand:
    *   Ensure `run(ctx, pctx, []string{"apply", biomeName}, verbose)` correctly invokes `apply`.

*   Refactor test setup in `internal/cmd/gocdk/main_test.go`:
    *   Create `newTestModule` helper function.
    *   Generate a temporary directory and write a `go.mod` file containing 'module example.com\n' at its root.
    *   Return the directory path, a cleanup function to remove the directory, and any error.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.