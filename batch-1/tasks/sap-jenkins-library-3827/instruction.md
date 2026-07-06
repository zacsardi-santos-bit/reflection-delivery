Update the Helm execution component to support remote chart repositories and improve error handling for uninstall operations. Implement the following changes to enhance flexibility and clarity in Helm chart management.

*   RunHelmUpgrade:
    *   Use `ChartPath` directly if provided; skip repository registration.
    *   If `ChartPath` is empty, call `runHelmAdd()` and use `TargetRepositoryName` as the chart reference.
    *   Do not return an error solely due to an empty `ChartPath`.

*   RunHelmInstall:
    *   Use `ChartPath` directly if provided; skip repository registration.
    *   If `ChartPath` is empty, call `runHelmAdd()` and use `TargetRepositoryName` as the chart reference.
    *   Do not return an error solely due to an empty `ChartPath`.
    *   If verbose mode is enabled and `KeepFailedDeployments` is false, perform a dry-run install first, followed by the actual install.

*   RunHelmUninstall:
    *   Do not call `runHelmAdd()` at any point.
    *   Return the error "namespace has not been set, please configure namespace parameter" if `Namespace` is empty.
    *   If verbose mode is enabled and `HelmDeployWaitSeconds` is set, perform a dry-run uninstall first, followed by the actual uninstall.

*   Repository Registration:
    *   Include `--username` and `--password` flags in the `repo add` command if `TargetRepositoryUser` and `TargetRepositoryPassword` are provided.
    *   Append `--debug` to the `repo add` command if the verbose flag is true.

*   RunHelmDependency:
    *   Return the error "there is no dependency value. Possible values are build, list, update" if no `Dependency` value is configured.
    *   Execute `helm dependency <Dependency> <ChartPath>` when a valid `Dependency` value is provided.

*   RunHelmLint:
    *   Execute `helm lint <ChartPath>` and return no error.

*   runHelmPackage:
    *   Execute `helm package <ChartPath>` and append `--version`, `--dependency-update`, and `--app-version` flags if those fields are set.

*   RunHelmTest:
    *   Execute `helm test <ChartPath>` and append `--filter` and `--logs` flags if `FilterTest` and `DumpLogs` are set.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.