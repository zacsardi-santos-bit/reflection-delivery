Implement support for encryption and decryption operations in container image manifest handling within the containers/image library. Ensure that encryption operations update the media type correctly in OCI manifests and that errors are returned for unsupported operations in Docker manifests. Handle format conversions with appropriate encryption and decryption logic.

*   UpdateLayerInfos method in `manifest/docker_schema1.go`:
    *   Return an error if any layer update has a non-zero CryptoOperation, as Docker schema1 does not support encrypted layers.
*   UpdateLayerInfos method in `manifest/docker_schema2.go`:
    *   Return an error if any layer update has a non-zero CryptoOperation, as Docker schema2 does not support encrypted layers.
*   UpdateLayerInfos method in `manifest/oci.go`:
    *   Append "+encrypted" to the media type for layers with CryptoOperation == types.Encrypt.
    *   Remove "+encrypted" from the media type for layers with CryptoOperation == types.Decrypt.
    *   Preserve layer annotations from updates.
*   UpdatedImage method in `internal/image/oci.go`:
    *   Return an error when converting an OCI image with encrypted layers to schema1 or schema2 without specifying a decrypt operation.
    *   Return an error when converting an OCI image to schema1 or schema2 while requesting encryption.
    *   Succeed in converting an OCI image with encrypted layers to schema1 or schema2 while providing CryptoOperation: Decrypt, ensuring the source image object is not mutated.
    *   Clone manifest data instead of modifying it in place during conversion.
*   Ensure the source image object remains unchanged after conversion operations involving decryption.
*   Create fixture files:
    *   `manifest/fixtures/ociv1.encrypted.manifest.json` with three encrypted layers.
    *   `internal/image/fixtures/oci1.encrypted.json` with five encrypted layers for OCI-to-Docker conversion tests.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.