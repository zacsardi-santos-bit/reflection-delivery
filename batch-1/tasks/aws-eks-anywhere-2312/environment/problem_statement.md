## Description

The git provider integration is missing the ability to programmatically add SSH deploy keys to GitHub repositories. This means end-to-end tests currently have to rely on externally pre-provisioned credentials (such as a private key stored in an external storage bucket) rather than generating fresh, isolated credentials per test run.

We need to extend the GitHub provider client so that it can create a deploy key on a GitHub repository given the owner, repository name, key content, title, and whether the key should be read-only. This will allow test setup code to generate a new SSH key pair, register the public key as a deploy key on a freshly created repository, and write the private key to the test instance — all without any pre-provisioned external state.

Additionally, the local git client's branch creation behavior is not correctly recording the rebase configuration for new branches. This causes a mismatch with expected behavior for consumers that rely on that configuration being present.

## Expected Behavior

- The git provider client interface should include a method for adding deploy keys to a repository, accepting the owner, repository name, public key content, a human-readable title, and a read-only flag.
- The GitHub-backed implementation of that interface should forward the request to the underlying GitHub API client with the appropriate parameters.
- When creating and tracking a new branch in the local git client, the stored branch configuration should include the rebase setting enabled.
- The test framework's environment variable constants for git/GitHub configuration should be exported so they are accessible from other packages.
- A function for returning the set of environment variables required for a git-with-repo-creation workflow should be available, distinct from the older function that assumed the repository URL was already known.

## Why This Matters

End-to-end tests should be able to set up a completely isolated GitHub repository — including SSH key generation and deploy key registration — for each test run, rather than depending on shared or externally stored credentials. Correct branch configuration ensures downstream tooling that reads branch settings behaves as expected.
