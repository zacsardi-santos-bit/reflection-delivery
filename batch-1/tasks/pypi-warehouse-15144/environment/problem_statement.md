## Description

PyPI's trusted publishing feature currently only supports GitHub Actions as an identity provider. Teams building and releasing Python packages via Google Cloud infrastructure have no equivalent option — they still need to manage long-lived API tokens or passwords instead of relying on short-lived, workload-specific credentials.

We should add Google Cloud as a supported trusted publisher so that project maintainers can register Google Cloud workload identities and use them to publish packages without storing persistent credentials.

## Expected Behavior

- Project maintainers can add, view, and remove Google Cloud trusted publishers for their projects, just as they can for GitHub Actions publishers.
- Account holders can register pending Google-based trusted publishers for new (not-yet-created) projects.
- The publishing management pages show whether each provider (GitHub and Google) is currently enabled or disabled by administrators, via a per-provider "disabled" status indicator.
- Google trusted publishing can be disabled by administrators independently of GitHub trusted publishing, using a dedicated admin flag.
- When a provider is administratively disabled, attempting to add a publisher for that provider shows a clear error message identifying the provider by name.
- All event logs and metrics correctly identify the publisher type as "Google" when performing Google-related trusted publishing operations.

## Why This Matters

Many Python package maintainers and organizations rely on Google Cloud for their CI/CD pipelines. Without support for Google Cloud trusted publishing, these teams are excluded from the security benefits that trusted publishing provides. Adding Google support makes PyPI's trusted publishing system more broadly useful and reduces the need to manage long-lived credentials for a large segment of the Python packaging community.
