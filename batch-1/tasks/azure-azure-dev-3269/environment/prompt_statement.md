I'm working with a cloud developer CLI that manages containerized service deployments. Right now it seems like I can only deploy services by building container images from source code — there's no way to point it at an existing image from Docker Hub or another registry and have it deploy that instead. I need support for deploying services using pre-built images.

On top of that, the container image configuration is too coarse-grained. Currently it looks like the image name and tag are combined into one field, which means I can't separately control the registry, image name, and tag for different environments. I'd like to be able to configure each of these independently, including using environment variable references, so I can customize the image reference per environment without duplicating configuration.

Specifically, I need the tool to:
- Allow specifying a pre-existing container image as the service source instead of always building from source
- When no container registry is configured and a pre-built image is used, skip the push step and use the image directly
- When a registry is configured, pull the pre-built image, tag it for the registry, and push it
- Expose separate configuration options for the registry, image name, and tag (all supporting environment variable substitution)
- Handle the case where a service has a pre-built image configured but no source project — this should resolve correctly to the container deployment workflow
- Fail clearly when a containerized service has neither a source project nor a pre-built image configured

There should also be a way to parse a container image reference string into its component parts (registry, repository, and tag) and convert it back to both a local reference (without registry) and a fully-qualified remote reference (with registry).
