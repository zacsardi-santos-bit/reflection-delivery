## Description

The project's documentation and README files contain embedded links to interactive demos hosted on an external platform. One of these links was accidentally pointing to a stale feature branch instead of the main branch of the repository. This means that users who follow that link will be shown code from an older branch that may no longer be maintained, rather than the current version on main.

Additionally, there is no automated check to prevent incorrectly formatted demo links from being added to documentation in the future.

## Expected Behavior

- All demo links embedded in documentation files and package READMEs should consistently point to the main branch of the repository's official examples directory.
- An automated audit should scan the documentation files and enforce that any such demo links follow the correct, standardized format.

## Why This Matters

Without this fix, readers of the collaboration documentation are directed to an outdated demo. Without the automated check, similar mistakes could silently slip into future pull requests. This ensures a consistent and correct experience for anyone following demo links in the documentation.
