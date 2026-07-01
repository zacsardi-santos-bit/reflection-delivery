## Description

When a project has a git dependency declared with a branch specifier, the package identifier reported by cargo for that dependency includes a URL query string component encoding the branch information. However, passing this package identifier directly to the package selection flag of a build or check command fails — cargo incorrectly interprets the question mark in the URL as a glob wildcard character, which leads to a "no package matching" error instead of building the intended package.

## Expected Behavior

- Running the package ID lookup command for a git dependency with a branch specifier should produce an identifier containing the git repository URL with the branch name encoded as a query string parameter and the package version as a URL fragment.
- That identifier should be directly usable with the package selection flag. Cargo should recognize it as a valid package specifier and build the package successfully.
- The question mark in a URL-based package identifier must not be interpreted as a glob wildcard.

## Steps to Reproduce

1. Add a git dependency with a branch specifier to a project.
2. Generate the lockfile.
3. Look up the package ID for that git dependency.
4. Pass the resulting package ID directly to a build command using the package selection flag.
5. Cargo fails with a glob-matching error instead of building the package.

## Why This Matters

Users reasonably expect to be able to obtain a package's canonical identifier and use it immediately to build that package. Git dependencies with branch or other URL-encoded specifiers are common, and the workaround of manually editing the package ID before use is error-prone and unexpected.
