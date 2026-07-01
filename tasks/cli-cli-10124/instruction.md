Implement a new subcommand in the GitHub CLI to list autolink references configured for a repository. Ensure the command displays autolinks in a formatted table for interactive terminals, supports JSON output, and can open the repository's autolinks settings page in a browser.

*   Define the `autolink` struct in `pkg/cmd/repo/autolink/list/http.go`:
    *   Fields: `ID` (int), `IsAlphanumeric` (bool), `KeyPrefix` (string), `URLTemplate` (string).

*   Implement `AutolinkLister` in `pkg/cmd/repo/autolink/list/http.go`:
    *   Method: `List(repo ghrepo.Interface) ([]autolink, error)`
        *   Makes a GET request to `repos/{owner}/{repo}/autolinks`.
        *   Returns `([]autolink, nil)` on success.
        *   Returns error with message "error getting autolinks: HTTP 404: Perhaps you are missing admin rights to the repository? (https://api.github.com/repos/{OWNER}/{REPO}/autolinks)" on HTTP 404.

*   Implement `listOptions` struct in `pkg/cmd/repo/autolink/list/list.go`:
    *   Fields: `WebMode` (bool), `Exporter` (cmdutil.Exporter), `IO` (*iostreams.IOStreams), `Browser` (browser interface), `BaseRepo` (func() (ghrepo.Interface, error)), `AutolinkClient` (interface with method `List`).

*   Implement `NewCmdList` function in `pkg/cmd/repo/autolink/list/list.go`:
    *   Signature: `NewCmdList(f *cmdutil.Factory, runF func(*listOptions) error) *cobra.Command`
    *   Supports `--web` flag; sets `listOptions.WebMode = true`.
    *   Supports `--json` flag with valid fields: `id`, `isAlphanumeric`, `keyPrefix`, `urlTemplate`.
    *   Returns error "Unknown JSON field: \"<name>\"\nAvailable fields:\n  id\n  isAlphanumeric\n  keyPrefix\n  urlTemplate" for invalid JSON fields.

*   Implement `listRun` function in `pkg/cmd/repo/autolink/list/list.go`:
    *   Signature: `listRun(opts *listOptions) error`
    *   In web mode, opens browser to `https://github.com/{owner}/{repo}/settings/key_links` and writes "Opening https://github.com/{owner}/{repo}/settings/key_links in your browser." to stderr.
    *   In TTY mode with results, prints table with header "\nShowing N autolink references in OWNER/REPO\n\n" and columns: ID, KEY PREFIX, URL TEMPLATE, ALPHANUMERIC.
    *   In non-TTY mode, prints tab-separated values (id, keyPrefix, urlTemplate, isAlphanumeric) with no header.
    *   In JSON mode, exports results as a JSON array with only the selected fields.
    *   Returns `cmdutil.NewNoResultsError("no autolinks found in OWNER/REPO")` when no autolinks are found.
    *   Propagates errors from `AutolinkClient.List` unchanged.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.