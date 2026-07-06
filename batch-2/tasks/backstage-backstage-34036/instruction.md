I'm working on the GitHub repository publishing scaffolder action in a Backstage plugin.

*   When the initial git push fails with a connection-level error (error code or error.cause.code is ECONNRESET or ECONNREFUSED), the publish:github action must fall back to pushing files via the GitHub GraphQL API rather than failing, using a mutation whose string contains 'createCommitOnBranch'.

*   The GraphQL fallback must call octokit.rest.git.getRef({ owner, repo, ref: 'heads/<defaultBranch>' }) to obtain the current HEAD SHA and pass it as expectedHeadOid in the commit mutation input.

*   The GraphQL mutation input must have the structure: { branch: { repositoryNameWithOwner: 'owner/repo', branchName }, expectedHeadOid, fileChanges: { additions: [{ path, contents }], deletions?: [{ path }] } }.

*   When getRef fails with HTTP status 404 and octokit.rest.repos.get({ owner, repo }) also fails with status 404, the fallback must throw the error without calling createOrUpdateFileContents or the GraphQL API.

*   When getRef fails with HTTP status 404 and the repository exists but is non-empty (size > 0), the fallback must throw an error whose message contains "Branch '<branchName>' not found".

*   When getRef fails with HTTP status 404 and the repository is empty (size === 0), or when getRef fails with HTTP status 409, the fallback must initialize the repository by calling octokit.rest.repos.createOrUpdateFileContents with path '.gitkeep', use the returned commit SHA as expectedHeadOid, and include { path: '.gitkeep' } in fileChanges.deletions.

*   File collection for the GraphQL fallback must recursively traverse the workspace directory, skipping the '.git' directory entirely, skipping symbolic links, and skipping filesystem entries that are neither regular files nor directories.

*   Relative file paths for nested files must be correctly preserved (e.g., a file under src/utils/ should appear as 'src/utils/helper.ts' in the additions array).

*   When the GraphQL mutation call fails with an error message containing 'expectedHeadOid', the fallback must retry exactly once: re-fetch the HEAD ref via getRef and then re-issue the GraphQL mutation call (total of 2 GraphQL calls).

*   When initRepoAndPush fails with a non-connection error (error code is not ECONNRESET or ECONNREFUSED, and error.cause.code is also not ECONNRESET or ECONNREFUSED), the error must be rethrown without invoking the GraphQL fallback.

*   Upon successful completion of the GraphQL fallback, the action must set the output named 'commitHash' to the oid value from the GraphQL response's createCommitOnBranch.commit.oid field.

*   When initRepoAndPush completes successfully (no connection-level error), the action must continue to behave as before — the fallback must not interfere with the normal code path.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.