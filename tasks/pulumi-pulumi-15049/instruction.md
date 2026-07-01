Refactor the Pulumi Automation API in Go to allow for custom CLI implementations and improve version validation logic. Implement interfaces and functions to inject custom CLI implementations, handle version checks, and manage CLI paths effectively.

*   Define the `PulumiCommand` interface in `sdk/go/auto/cmd.go` with:
    *   `Version() semver.Version`
    *   `Run(ctx context.Context, workdir string, stdin io.Reader, additionalOutput []io.Writer, additionalErrorOutput []io.Writer, additionalEnv []string, args ...string) (string, string, int, error)`

*   Implement the `PulumiCommandOptions` struct in `sdk/go/auto/cmd.go` with fields:
    *   `Version semver.Version`
    *   `Root string`
    *   `SkipVersionCheck bool`
    *   Add a `withDefaults()` method to return a new copy with:
        *   `Version` defaulting to `sdk.Version` if zero
        *   `Root` defaulting to `path.Join(homeDir, '.pulumi', 'versions', Version.String())` if empty

*   Create the `fixupPath(env []string, pulumiBin string) []string` function in `sdk/go/auto/cmd.go` to:
    *   Return a modified copy of `env` with `pulumiBin` prepended to the `PATH`
    *   Append `PATH=<pulumiBin>` if no `PATH` exists
    *   Replace existing `PATH` with `PATH=<pulumiBin><os.PathListSeparator><existingPath>`

*   Develop the `parseAndValidatePulumiVersion(minVersion semver.Version, currentVersion string, optOut bool) (semver.Version, error)` function in `sdk/go/auto/cmd.go` to:
    *   Return an error with "Unable to parse" if `currentVersion` is invalid semver and `optOut` is false
    *   Return an error with "Major version mismatch." if the major version exceeds `minVersion.Major`
    *   Return an error with "Minimum version requirement failed." if the version is below `minVersion`, treating prerelease versions as lower
    *   Return no error if `optOut` is true

*   Implement `NewPulumiCommand(opts *PulumiCommandOptions) (PulumiCommand, error)` in `sdk/go/auto/cmd.go` to:
    *   Accept `nil` `opts` as empty
    *   Return an error with "version requirement failed" if CLI version is incompatible with `opts.Version` and `SkipVersionCheck` is false
    *   Skip version validation if `opts.SkipVersionCheck` is true

*   Implement the `Pulumi(pulumi PulumiCommand) LocalWorkspaceOption` function in `sdk/go/auto/local_workspace.go` to:
    *   Return a `LocalWorkspaceOption` using the provided `PulumiCommand` for CLI interactions

*   Modify `NewLocalWorkspace` to:
    *   Return an error with "does not support remote operations" if called with `remote(true)` and CLI lacks support
    *   Bypass remote operations support check if `EnvVars` contains `PULUMI_AUTOMATION_API_SKIP_VERSION_CHECK` set to a truthy value

*   Update the `Workspace` interface in `sdk/go/auto/workspace.go` to include:
    *   `PulumiCommand() PulumiCommand`

*   Implement the `PulumiCommand()` method on `LocalWorkspace` in `sdk/go/auto/local_workspace.go` to:
    *   Return the `PulumiCommand` instance stored in the `pulumiCommand` field

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.