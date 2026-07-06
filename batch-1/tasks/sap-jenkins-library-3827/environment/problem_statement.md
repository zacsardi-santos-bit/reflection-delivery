## Description

The Helm execution component currently requires a local chart path to be specified for both install and upgrade operations. If no local chart path is given, these operations fail with an error. This is too restrictive: many real-world deployments work by pointing at a remote chart repository and using the repository-registered name as the chart reference, without needing a locally checked-out copy of the chart.

Additionally, the uninstall operation currently registers a chart repository before proceeding, which is unnecessary — uninstall does not use the chart source at all. This causes spurious failures when repository credentials are not configured but users just want to uninstall a release.

Finally, the uninstall operation does not properly validate that a namespace has been configured before executing, which can lead to confusing runtime errors instead of a clear, early validation message.

## Expected Behavior

- If a local chart path is provided for upgrade or install, use it directly (and skip the repository registration step).
- If no local chart path is provided for upgrade or install, automatically register the configured remote repository and use the repository name as the chart reference.
- The uninstall operation must not attempt to register a chart repository.
- The uninstall operation must return a clear error early if no namespace has been configured.

## Why This Matters

Users deploying from remote chart registries are blocked by the mandatory local chart path requirement. Removing this restriction and supporting remote-repository-based deployments as a first-class option makes the tool significantly more flexible without breaking existing workflows that use a local chart path.
