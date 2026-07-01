Implement support for deploying containerized services using pre-built images in the developer CLI. Update the configuration to allow separate control over the registry, image name, and tag, enabling environment-specific customization.

*   Introduce a new `ContainerImage` type in the `docker` package:
    *   Fields: `Registry`, `Repository`, `Tag` (all strings).
    *   Method `Remote()`: Return full image reference as 'registry/repository:tag'.
    *   Method `Local()`: Return image reference as 'repository:tag', omitting registry.

*   Implement `ParseContainerImage` function:
    *   Accepts a container image string, returns `*ContainerImage` and error.
    *   Return error for empty string, tag-only input, multiple colons in image name, hostname-only input without path, and hostname-with-port-only input without path.
    *   Correctly parse multi-part repositories.

*   Modify `dockerPackageResult` struct:
    *   Rename `ImageTag` to `TargetImage`.
    *   Add `SourceImage` field to record external source image.

*   Update `DockerProjectOptions` struct:
    *   Add `Image` and `Registry` fields as `ExpandableString`.

*   Add `Image` field to `ServiceConfig` struct for pre-built source image specification.

*   Implement `GeneratedImage` method in `ContainerHelper`:
    *   Return `*docker.ContainerImage` based on service's docker configuration.
    *   Handle default values and error cases for malformed strings and invalid tags.

*   Implement `RemoteImageTag` method in `ContainerHelper`:
    *   Parse `localImageTag` and replace registry if configured.
    *   Handle errors when no registry is configured.

*   Update `DockerProject` to support packaging modes:
    *   Source-code mode: Use build output image ID.
    *   External-image mode: Use `docker pull` for source image, set `SourceImage` and `TargetImage`.

*   Implement `SetSource` method in `DockerProject`:
    *   Configure source framework for source-code builds.

*   Add `Pull` method to Docker interface:
    *   Execute "docker pull <imageName>".

*   Update `Deploy` method in `ContainerHelper`:
    *   Return `*ServiceDeployResult` with `RemoteImageTag`.
    *   Handle deployment logic based on registry configuration and source image presence.

*   Ensure `ServiceManager.GetFrameworkService` resolves correctly:
    *   Use 'docker' framework when `serviceConfig.Image` is set and `serviceConfig.RelativePath` is empty.
    *   Panic when neither image nor project path is set.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.