Implement a function to compute a unique identifier for container image layers, allowing for independent testing of this logic. Additionally, ensure that the system verifies the consistency of uncompressed-layer-digest values declared in an image's configuration against the actual uncompressed content of each layer.

*   Implement the `layerID` function in the `storage` package (`storage/storage_dest.go`):
    *   Signature: `layerID(parentID string, trusted trustedLayerIdentityData) string`
    *   Return `trusted.diffID.Encoded()` when `trusted.layerIdentifiedByTOC` is false and `parentID` is empty.
    *   Return `digest.Canonical.FromString(parentID + "+" + trusted.diffID.Encoded()).Encoded()` when `trusted.layerIdentifiedByTOC` is false and `parentID` is non-empty.
    *   Return `digest.Canonical.FromString(parentID + "+" + "@TOC=" + trusted.tocDigest.Encoded()).Encoded()` when `trusted.layerIdentifiedByTOC` is true, always hashing even if `parentID` is empty.
    *   Ensure `trusted.blobDigest` does not affect the result.
    *   Ignore `trusted.tocDigest` when `trusted.layerIdentifiedByTOC` is false.
    *   Ignore `trusted.diffID` when `trusted.layerIdentifiedByTOC` is true.

*   Define the `trustedLayerIdentityData` struct in the `storage` package (`storage/storage_dest.go`):
    *   Fields:
        *   `layerIdentifiedByTOC bool`
        *   `diffID digest.Digest`
        *   `tocDigest digest.Digest`
        *   `blobDigest digest.Digest`

*   Ensure the system validates DiffID values in image configurations:
    *   Reject images whose configuration DiffIDs do not match the actual uncompressed content of the layers being committed.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.