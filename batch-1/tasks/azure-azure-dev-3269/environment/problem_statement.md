## Description

The developer CLI for containerized services currently only supports building container images from local source code. There is no way to specify a pre-built container image (such as one from Docker Hub or a CI/CD pipeline) as the deployment source. Users who want to deploy an existing image must work around this limitation manually.

Additionally, the container image configuration conflates the image name and tag into a single field, making it impossible to independently control the registry, image repository name, and tag. This becomes problematic when different environments need different registries or when teams want to drive image references from environment variables.

## Expected Behavior

- Users should be able to specify a pre-built container image as their service source, in addition to building from source code.
- When a pre-built image is specified without a container registry configured, the tool should use the image directly without any push operations.
- When a registry is configured, the tool should pull the pre-built image, re-tag it for the target registry, and push it.
- The configuration for a container service's image should support separate, independently configurable fields for the registry, image name, and tag — all supporting environment variable substitution.
- When a service specifies a pre-built image but no source code project, the tool should still correctly resolve and execute the appropriate framework lifecycle.
- Attempting to configure a service with neither source code nor a pre-built image should result in a clear failure.

## Why This Matters

Many teams use pre-built or externally managed container images rather than building from source on every deployment. Supporting this workflow allows the CLI to be used in a broader range of CI/CD patterns, including scenarios where image build and deployment are separate pipeline steps or where a public image is used without modification.
