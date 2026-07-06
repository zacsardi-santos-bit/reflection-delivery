## Description

When creating a new Pulumi project from a registry template, there is currently no way to specify which version of a template to use. The system always resolves to the latest available version, making it impossible to reproduce the exact project scaffold from a specific point in time or to use a known-good version that matches your requirements.

## Expected Behavior

- Users should be able to append a version specifier (e.g., "@1.0.0") to any template identifier when creating a project.
- This works for all identifier formats: a fully-qualified source/publisher/name reference, a publisher/name shorthand, or a bare template name.
- If the specified version exists, that version of the template is fetched and used.
- If the specified version does not exist, the user receives a clear error message identifying the missing version.
- Templates backed by version control repositories (like GitHub) must explicitly reject version specifiers with a descriptive error explaining that this mechanism is not supported for such templates.

## Internals

A general-purpose template resolution function should be introduced that accepts a template identifier in single-name, publisher-qualified, or fully-qualified form, together with an optional version constraint. The function should follow a clear resolution strategy depending on how many parts the identifier has, and return a structured error when the identifier is malformed (too many parts) or when the requested version is not available.

## Why This Matters

Without version pinning, teams cannot reliably recreate the same project scaffold over time. Pinning to a specific template version ensures reproducibility and gives users control over when to adopt changes introduced in newer template versions.
