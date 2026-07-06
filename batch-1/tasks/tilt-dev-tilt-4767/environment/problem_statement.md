## Description

Tilt users have no way to attach custom organizational labels to their resources (Kubernetes, Docker Compose, or local) via the Tiltfile. Adding support for user-defined labels would allow users to categorize and group their resources in the Tilt UI, making it easier to navigate large projects with many services.

## Expected Behavior

- Users should be able to specify one or more labels on any resource using a dedicated parameter in the resource configuration functions
- Labels can be provided as a single string or as a list of strings
- Multiple configuration calls for the same resource should accumulate labels additively (labels from each call are merged together)
- Label names must be validated: they must follow standard naming rules (alphanumeric characters, non-empty), and invalid names should produce a descriptive error
- Changing a resource's labels should **not** trigger a rebuild — labels are organizational metadata, not build-affecting configuration

## Why This Matters

As projects grow, users need better ways to organize and filter resources in the Tilt UI. User-defined labels on resources provide a flexible mechanism for grouping services by team, tier, or any other organizational dimension — without affecting how resources are built or deployed.
