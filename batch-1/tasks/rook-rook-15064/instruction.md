Implement the ability to configure which protocols the RADOS Gateway exposes in the Rook Ceph object store operator. Ensure that the operator can selectively disable S3 and expose only the desired subset of gateway APIs. Adapt the gateway health probe path based on the active protocols.

*   Implement the `buildRGWEnableAPIsConfigVal` function in `pkg/operator/ceph/object/spec.go`:
    *   Accept a `cephv1.ProtocolSpec` parameter.
    *   Return `nil` if no API configuration is specified.
    *   Return a trimmed slice of API names from `EnableAPIs` if set, taking precedence over other settings.
    *   Return the `rgwAPIwithoutS3` slice if `S3.Enabled` is `false` or `Swift.UrlPrefix` is set to `'/'`.
    *   Return `nil` when `Swift.UrlPrefix` is set to any value other than `'/'`.

*   Implement the `buildRGWConfigFlags` function in `pkg/operator/ceph/object/spec.go`:
    *   Accept a `*cephv1.CephObjectStore` parameter.
    *   Return `nil` if no protocol configuration is present.
    *   Return a slice containing `"--rgw-enable-apis=<comma-separated-api-list>"` when `EnableAPIs` is configured.

*   Implement the `getRGWProbePath` function in `pkg/operator/ceph/object/spec.go`:
    *   Accept a `cephv1.ProtocolSpec` parameter.
    *   Return `("", false)` if S3 is effectively enabled.
    *   Return `("/swift/info", false)` if S3 is disabled and Swift is available without a custom prefix.
    *   Return `("/<prefix>/info", false)` if S3 is disabled and Swift has a custom `UrlPrefix`.
    *   Return `("/info", false)` if `Swift.UrlPrefix` is `'/'`.
    *   Return `("", true)` if neither S3 nor Swift is active.

*   Define a package-level variable `rgwAPIwithoutS3` in `pkg/operator/ceph/object/spec.go`:
    *   Type: `[]string`
    *   Represents the list of all supported RGW APIs excluding S3.

*   Update the `cephv1` package types:
    *   Define `ProtocolSpec` struct with fields: `EnableAPIs []ObjectStoreAPI`, `S3 *S3Spec`, `Swift *SwiftSpec`.
    *   Define `S3Spec` struct with field: `Enabled *bool`.
    *   Define `SwiftSpec` struct with field: `UrlPrefix *string`.
    *   Define `ObjectStoreAPI` as a string-based type.
    *   Add `Protocols` field of type `ProtocolSpec` to `ObjectStoreSpec`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.