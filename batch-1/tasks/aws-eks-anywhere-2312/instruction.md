Implement the ability to programmatically add SSH deploy keys to GitHub repositories in the git provider client. Update the local git client's branch creation behavior to correctly record the rebase configuration for new branches. Export necessary constants for git/GitHub configuration and provide a function for environment variables required for a git-with-repo-creation workflow.

*   Define a struct `AddDeployKeyOpts` in `pkg/git/` with the following fields:
    *   Owner (string)
    *   Repository (string)
    *   Key (string)
    *   Title (string)
    *   ReadOnly (bool)

*   Update the `GoGithub` struct in `pkg/git/gogithub/`:
    *   Add a method `AddDeployKeyToRepo(ctx context.Context, opts AddDeployKeyOpts) error`.
    *   Construct a `*github.Key` with pointers to `opts.Key`, `opts.Title`, and `opts.ReadOnly`.
    *   Delegate to the underlying client's `AddDeployKeyToRepo` with the owner, repository name, and `*github.Key`.

*   Modify the `Client` interface in `pkg/git/gogithub/`:
    *   Include `AddDeployKeyToRepo(ctx context.Context, owner, repository string, key *github.Key) error`.

*   Update the `ProviderClient` interface in `pkg/git/`:
    *   Add `AddDeployKeyToRepo(ctx context.Context, opts AddDeployKeyOpts) error`.

*   Fix branch creation in `pkg/git/gitclient/`:
    *   Ensure `config.Branch` includes `Rebase` set to "true".

*   Export constants in `test/framework/flux.go`:
    *   `GitRepositoryVar` (value: "T_GIT_REPOSITORY")
    *   `GitRepoSshUrl` (value: "T_GIT_SSH_REPO_URL")
    *   `GithubUserVar` (value: "T_GITHUB_USER")
    *   `GithubTokenVar` (value: "EKSA_GITHUB_TOKEN")
    *   `GitKnownHosts` (value: "EKSA_GIT_KNOWN_HOSTS")
    *   `GitPrivateKeyFile` (value: "EKSA_GIT_PRIVATE_KEY")

*   Update the function in `test/framework/flux.go`:
    *   Rename `RequiredFluxGitEnvVars` to `RequiredFluxGitCreateRepoEnvVars`.
    *   Return a slice containing `GitKnownHosts`, `GitPrivateKeyFile`, and `GithubUserVar`.

*   Update the `GithubClient` interface in `pkg/git/providers/github`:
    *   Add `AddDeployKeyToRepo(ctx context.Context, opts AddDeployKeyOpts) error`.

*   Implement the method on `githubProvider` struct in `pkg/git/providers/github`:
    *   `AddDeployKeyToRepo(ctx context.Context, opts AddDeployKeyOpts) error` should delegate to `g.githubProviderClient.AddDeployKeyToRepo(ctx, opts)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.