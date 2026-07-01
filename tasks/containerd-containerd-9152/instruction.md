Refactor the CRI image service in containerd to decouple it from the general plugin configuration, allowing it to be used and tested independently. Introduce a separate configuration structure for image-related settings and update the image service constructor to accept this new configuration. Implement additional changes to improve configurability and maintainability.

*   Create a new `ImageConfig` struct in `pkg/cri/config`:
    *   Fields: `Snapshotter string`, `Registry criconfig.Registry`, `ImagePullProgressTimeout string`, `StatsCollectPeriod int`, `PinnedImages map[string]string`.
    *   Replace the `SandboxImage` string field in `PluginConfig` with `PinnedImages`.

*   Implement `DefaultImageConfig()` in `pkg/cri/config`:
    *   Return a populated `ImageConfig` with default values.

*   Implement `ValidateImageConfig(ctx context.Context, config *ImageConfig)` in `pkg/cri/config`:
    *   Validate registry configuration, returning an error if both `mirrors` and `config_path` are set.
    *   Handle deprecated registry auth config migration.

*   Create `GRPCCRIImageService` struct in `pkg/cri/server/images`:
    *   Embed `*CRIImageService`.
    *   Ensure methods like `ImageStatus`, `ImageFsInfo`, and `ListImages` are callable.

*   Create `ImagePlatform` struct in `pkg/cri/server/images`:
    *   Fields: `Snapshotter string`, `Platform platforms.Platform`.

*   Create `CRIImageServiceOptions` struct in `pkg/cri/server/images`:
    *   Fields: `ImageFSPaths map[string]string`, `RuntimePlatforms map[string]ImagePlatform`, `Content content.Store`, `Images images.Store`, `Client *containerd.Client`.

*   Update `NewService` function in `pkg/cri/server/images`:
    *   Signature: `NewService(config criconfig.ImageConfig, opts *CRIImageServiceOptions) (ImageService, error)`.
    *   Ensure service is constructable without full CRI plugin config.

*   Update `CRIImageService` struct in `pkg/cri/server/images/service.go`:
    *   Change `config` field to `criconfig.ImageConfig`.
    *   Change `runtimePlatforms` field to `map[string]ImagePlatform`.

*   Modify `snapshotterFromPodSandboxConfig` method on `CRIImageService`:
    *   Return default snapshotter when runtime is not found.

*   Update `getLabels` method on `CRIImageService`:
    *   Use `config.PinnedImages` to determine pinned image labels.

*   Move `ParseImageReferences` function to `pkg/cri/util`:
    *   Signature: `ParseImageReferences(refs []string) (tags []string, digests []string)`.

*   Rename `imageService` field to `ImageService` in `criService` struct in `pkg/cri/server`.

*   Ensure `ImageService` interface in `pkg/cri/server` includes:
    *   Methods: `CheckImages`, `PullImage`, `RuntimeSnapshotter`, `UpdateImage`, `GetImage`, `GetSnapshot`, `LocalResolve`, `ImageFSPaths`.

*   Ensure `plugins/cri/images` package is importable as a plugin via blank import.

*   Update `PullImage` method on `CRIImageService`:
    *   Signature: `PullImage(ctx context.Context, image string, credentials func(string) (string, string, error), sandboxConfig *runtime.PodSandboxConfig) (string, error)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.