I'm working on Argo CD's repository server and running into an issue with how Helm application details are populated for multi-source applications.

*   The populateHelmAppDetails function must be converted from a package-level function to a method on the Service struct, with two new parameters added between repoRoot and q: sha string (the resolved commit SHA of the main application repo) and targetRevision string (the branch/revision name of the main application).

*   When the query's RefSources map contains git repositories that are actually referenced in the Helm source's ValueFiles (using the $refname/path/to/file.yaml syntax), the method must resolve and check out those repositories so their value files can be read.

*   Ref sources declared in RefSources but not referenced in any ValueFiles must NOT be checked out — no git operations should be performed for them.

*   When iterating over ref sources to check for conflicts and perform checkouts, the ref source names must be processed in lexicographically sorted order. This ensures deterministic error messages: when a conflict is found, the error reports the name being processed (alphabetically later) first and the previously-seen conflicting name second.

*   When checkout of a referenced repository fails (after retry), the method must return an error whose message contains: 'failed to acquire lock for referenced repo <repo_url>:' followed by the underlying error message.

*   When a ref source points to the same repository URL as the main application but its resolved SHA differs from the main application's sha parameter, the method must return an error whose message contains: 'cannot reference a different revision of the same repository (%s references %q which resolves to %q while the application references %q which resolves to %q' formatted with: the ref name, the ref's targetRevision, the ref's resolved SHA, the main app's targetRevision, and the main app's sha.

*   When two different ref source names point to the same external repository but resolve to different SHAs (conflicting revisions), the method must return an error whose message contains: 'cannot reference multiple revisions for the same repository (%s references %q which resolves to %q while %s references %q which resolves to %q' formatted with: the current (alphabetically later) ref name, its targetRevision, its resolved SHA, then the previously-seen (alphabetically earlier) ref name, its targetRevision, and its resolved SHA.

*   When LsRemote fails for a referenced repository's revision, the method must return an error whose message contains: 'error setting up git client for <repo_url> and resolving revision <revision>: <underlying_error>'.

*   For non-git referenced repositories (e.g. OCI type), the method must call GetPathIfExists (not GetPath) to check whether a cached path exists for the repository.


*   Interface details: Type: Method
Name: populateHelmAppDetails
Location: reposerver/repository/repository.go
Signature: populateHelmAppDetails(res *apiclient.RepoAppDetailsResponse, appPath string, repoRoot string, sha string, targetRevision string, q *apiclient.RepoServerAppDetailsQuery, tempPaths utilio.TempPaths) error
Description: Method on the Service struct (receiver type *Service) that populates Helm application details. Must be converted from its previous form as a package-level function to a method on Service. Two new parameters are added between repoRoot and q: sha (the resolved commit SHA of the main application's repository at the current targetRevision) and targetRevision (the branch name or revision string of the main application). The method must use these to validate and check out external referenced repositories listed in the query's RefSources when those repos are actually referenced in the Helm source's ValueFiles.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.