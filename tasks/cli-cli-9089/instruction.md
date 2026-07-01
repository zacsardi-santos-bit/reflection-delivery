Refactor the git credential management code in the GitHub CLI's auth package by extracting the credential helper configuration and credential update logic into a dedicated sub-package. Implement three focused types to handle credential helper representation, configuration, and updating. Update the existing credential flow type to integrate these new types and streamline the public API.

*   Create a new sub-package at `pkg/cmd/auth/shared/gitcredentials/` with the following types:
    *   `Helper` struct:
        *   Field: `Cmd string`
        *   Method: `IsConfigured() bool` - Return true if `Cmd` is non-empty.
        *   Method: `IsOurs() bool` - Return true if the command represents the gh CLI credential helper, specifically starting with `!` and referencing the gh executable with auth arguments.
    *   `HelperConfig` struct:
        *   Fields: `SelfExecutablePath string`, `GitClient *git.Client`
        *   Method: `ConfigureOurs(hostname string) error` - Register the gh CLI as the git credential helper in global git config for `hostname` and `gist.{hostname}`.
        *   Method: `ConfiguredHelper(hostname string) (Helper, error)` - Return the configured helper for a hostname, distinguishing between no helper, a third-party helper, and the CLI's helper.
    *   `Updater` struct:
        *   Field: `GitClient *git.Client`
        *   Method: `Update(hostname, username, password string) error` - Erase existing credentials and store new ones, ensuring subsequent lookups return updated values.

*   Update the `GitCredentialFlow` struct in `pkg/cmd/auth/shared/git_credential.go`:
    *   Change `helper` field type from `string` to `gitcredentials.Helper`.
    *   Remove `Executable string` and `GitClient *git.Client` fields.
    *   Add fields: `Updater *gitcredentials.Updater`, `HelperConfig *gitcredentials.HelperConfig`.
    *   Rename method `gitCredentialSetup` to `Setup(hostname, username, authToken string) error`.

*   Modify the options struct for the setup-git command in `pkg/cmd/auth/setupgit/setupgit.go`:
    *   Rename field `gitConfigure` to `CredentialsHelperConfig`.
    *   Ensure `CredentialsHelperConfig` satisfies only the method: `ConfigureOurs(hostname string) error`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.