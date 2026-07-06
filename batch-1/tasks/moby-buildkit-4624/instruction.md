Implement a per-mount content-based cache control mechanism in BuildKit's exec operation solver. Ensure that each mount can explicitly enable, disable, or auto-detect the safety of content caching, following specific rules for when caching is safe or should be disabled.

*   Update the `CacheMap` method in `solver/llbsolver/ops/exec.go`:
    *   Support a per-mount content-cache setting to control the assignment of `ComputeDigestFunc` to dependency entries.
    *   When `ContentCache` is set to `pb.MountContentCache_OFF`, ensure `CacheMap` succeeds without assigning `ComputeDigestFunc`.
    *   When `ContentCache` is set to `pb.MountContentCache_ON` and the mount is safe, ensure `CacheMap` assigns a non-nil `ComputeDigestFunc`.
        *   A mount is safe if it has a root selector ('/'), is read-only, or is marked no-output.
    *   Return `ok=false` and an error containing "invalid mount" when `ContentCache` is `ON` but the mount is unsafe (writable, non-root selector).
    *   In default mode (`pb.MountContentCache_DEFAULT`), assign `ComputeDigestFunc` if the mount is not at root destination and is safe.
    *   Do not assign `ComputeDigestFunc` in default mode if the mount destination is '/'.

*   Modify the protobuf definition in `solver/pb/ops.pb.go`:
    *   Add a `ContentCache` field to the `pb.Mount` struct of type `pb.MountContentCache`.
    *   Ensure `pb.MountContentCache` enum includes:
        *   `MountContentCache_DEFAULT` with value 0 for auto-detection.
        *   `MountContentCache_ON` with value 1 to enable caching (with error on unsafe config).
        *   `MountContentCache_OFF` with value 2 to disable caching.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.