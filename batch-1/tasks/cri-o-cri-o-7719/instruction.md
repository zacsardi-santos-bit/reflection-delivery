Implement support for loading seccomp profiles from OCI artifacts in CRI-O by utilizing annotations to pull profiles from container registries. Ensure the system can handle image-level, pod-wide, and container-specific annotations, and maintain existing behavior when no annotations are present.

*   Define the `SeccompOCIArtifact` struct in `internal/config/seccomp/seccompociartifact`.
    *   Implement the `New()` function to return a non-nil `*SeccompOCIArtifact`.
    *   Implement the `SetOCIArtifactImpl` method for test builds to inject an `ociartifact.Impl` implementation.
*   Implement the `TryPull` method on `*SeccompOCIArtifact` with the signature:
    ```go
    TryPull(ctx context.Context, sys *types.SystemContext, containerName string, podAnnotations map[string]string, imageAnnotations map[string]string) ([]byte, error)
    ```
    *   Return `(nil, nil)` if both `podAnnotations` and `imageAnnotations` are nil or empty.
    *   For `imageAnnotations` with key `annotations.SeccompProfileAnnotation`, call `Pull` on the OCI artifact, find a JSON file, and return its contents.
    *   For `podAnnotations` with key `annotations.SeccompProfileAnnotation`, call `Pull`, find a JSON file, and return its contents.
    *   For `podAnnotations` with a container-specific key, call `Pull` and return JSON file contents if the key matches `containerName`.
    *   Return `(nil, nil)` if a container-specific annotation key does not match `containerName`.
    *   Return `(nil, error)` if the `Pull` call fails or if no JSON file is found.
*   Define the `ociartifact.Impl` interface in `internal/config/ociartifact` with:
    ```go
    Pull(ctx context.Context, sys *types.SystemContext, ref string) (*Artifact, error)
    ```
*   Define the `ociartifact.Artifact` struct with:
    *   `MountPath string` for the directory containing artifact files.
    *   `Cleanup func()` for unmounting the directory.
*   Update the `Setup` function in `internal/config/seccomp` with the new signature:
    ```go
    Setup(ctx context.Context, sys *types.SystemContext, msgChan chan Notification, containerID string, containerName string, sandboxAnnotations map[string]string, imageAnnotations map[string]string, specGenerator *generate.Generator, profileField *types.SecurityProfile) (*Notifier, string, error)
    ```
*   Add the constant `SeccompProfileAnnotation` to `pkg/annotations/annotations.go` with the value `"io.kubernetes.cri-o.seccompProfile"` and include it in `AllAllowedAnnotations`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.