## Description

When a changeset-based release merges to the main branch, some packages in our monorepo are public and automatically published to npm, but others are private and need a different deployment mechanism (e.g. Cloudflare Workers or Pages projects). Right now, these private packages each have their own separate CI workflow to handle deployment, which is hard to maintain and doesn't integrate with our changeset flow.

We need a unified tool that, after a release, reads the list of packages that were bumped, identifies which ones are private and deployable (i.e. have a deploy script), and triggers their deployment automatically. Packages that were already handled via npm should be skipped with an informative message.

## Expected Behavior

- A new script/tool can be run as part of the CI release process to deploy non-npm packages.
- The tool reads the list of updated packages from a well-known environment variable.
- It validates the input — if the data is malformed, it should fail with a clear, descriptive error message.
- It determines which packages are "deployable" by scanning the monorepo for private packages that have a deploy script.
- For each updated package, it logs whether it's being deployed or skipped (because it's already on npm).
- If a deployment fails, the error is logged and the process continues for remaining packages rather than aborting entirely.
- A summary of how many packages were deployed is printed at the end.

## Why This Matters

This eliminates the need for per-package deployment workflows and ties private package deployments into the existing changeset release flow, making the CI pipeline more maintainable and consistent.
