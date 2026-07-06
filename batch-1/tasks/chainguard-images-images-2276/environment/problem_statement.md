## Add Grafana Agent Operator container image

### Description

The Grafana Agent Operator is a Kubernetes operator for managing Grafana Agent in static mode. We should add a hardened container image for it to this repository so users can pull a security-focused version from our registry instead of relying on upstream images.

### Expected Behavior

- A new image directory for the Grafana Agent Operator should exist with the full standard layout (build configuration, APK package template, image metadata, documentation, and test infrastructure).
- The image should be discoverable by the repository's automation tools so it appears in CI build matrices and passes all linting checks.
- The image's documentation should follow the repository's standard format so it renders correctly.
- The root module configuration should register the new image so it is built and published as part of the normal pipeline.
- The pre-existing tests directory should be wired into the image's build configuration so deployment verification runs as part of image promotion.

### Why This Matters

Without this image, users running Grafana Agent Operator on Kubernetes have no hardened, regularly-updated alternative from this registry. Adding the image brings it under the same security scanning, rebuild, and distribution pipeline as all other images in this repository.
