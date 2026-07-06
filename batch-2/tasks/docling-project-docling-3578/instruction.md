Enhance the document conversion client to support pre-signed download URLs. Implement fallback mechanisms and security checks to ensure safe and reliable artifact handling. Ensure the client can handle both inline and external artifact storage configurations seamlessly.

*   Implement the function `_is_safe_artifact_url(url: str) -> bool` in `docling/service_client/client.py`:
    *   Return `True` only if the URL uses `http` or `https` and the host is globally routable.
    *   Return `False` for private, loopback, link-local addresses, non-http/https schemes, and invalid URLs.

*   Create a new exception class `ArtifactDownloadError` in `docling/service_client/exceptions.py`:
    *   Inherit from `DoclingServiceClientError`.
    *   Add to `__all__` in `docling/service_client/__init__.py` for direct import.

*   Implement methods in `DoclingServiceClient` in `docling/service_client/client.py`:
    *   `_download_artifact_bytes(self, uri: str) -> bytes`: Synchronously download artifacts, raising `ArtifactDownloadError` on failure.
    *   `async _download_artifact_bytes_async(self, uri: str) -> bytes`: Asynchronously download artifacts for batch conversion.

*   Update `DoclingServiceClient.convert()`:
    *   Attempt conversion with `PresignedUrlTarget` first, falling back to `InBodyTarget` on `ServiceError`.
    *   Download the preferred `resource_bundle` ZIP if available, otherwise download the `json` artifact.
    *   Extract and embed images from the resource bundle into the document.

*   Ensure error handling and security:
    *   Raise `ArtifactDownloadError` for download failures or security violations.
    *   Preserve server error messages in `ConversionStatus.FAILURE` results.
    *   Reject non-public artifact URLs and path traversal attempts with descriptive error messages.

*   Enhance `DoclingServiceClient.convert_all()`:
    *   Support presigned artifact materialization using `_submit_and_retrieve_many_async` with `target=None`.
    *   Maintain input order in results, embedding images for presigned responses.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.