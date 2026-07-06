Implement a feature to preserve and expose token usage information from API responses in the docling picture description pipeline. Ensure that usage data is accessible in the output document, supporting non-standard and nested JSON keys. Handle malformed or empty API responses gracefully and configure consistent HTTP retry logic.

*   Update `api_image_request` in `docling/utils/api_image_request.py`:
    *   Return an `ApiImageRequestResult` object with attributes: `.text` (str), `.num_tokens` (int or None), `.stop_reason` (VlmStopReason), and `.usage` (dict or None).
    *   Accept keyword-only parameters `usage_response_key` (default 'usage') and `token_extract_key` (default None) for specifying JSON keys, supporting dotted paths.
    *   Set `.usage` to the resolved key value in the API response JSON. Set `.num_tokens` to `total_tokens` from the usage dict or fallback to `usage.total_tokens`.
    *   Log 'API response body was empty' with 'status=<status_code>' for empty responses, returning `ApiImageRequestResult('', 0, VlmStopReason.UNSPECIFIED)`.
    *   Log 'API response body was not JSON' with a preview for non-JSON responses, returning `ApiImageRequestResult('', 0, VlmStopReason.UNSPECIFIED)`.

*   Update `api_image_request_streaming` in `docling/utils/api_image_request.py`:
    *   Return an `ApiImageStreamingRequestResult` object with attributes: `.text` (str), `.num_tokens` (int or None), and `.usage` (dict or None).
    *   Support `usage_response_key` and `token_extract_key` parameters for usage extraction from SSE chunks.
    *   Preserve usage data seen before early stopping triggered by `generation_stoppers`.

*   Implement `_make_retry_session` in `docling/utils/api_image_request.py`:
    *   Provide a context manager yielding a `requests.Session` with retry configuration: total=5, connect=5, read=0, status=5, backoff_factor=0.1, status_forcelist={429, 500, 502, 503, 504}, allowed_methods={'POST'}, respect_retry_after_header=True.

*   Define `ApiImageRequestResult` and `ApiImageStreamingRequestResult` as frozen dataclasses in `docling/datamodel/base_models.py`:
    *   `ApiImageRequestResult`: `ApiImageRequestResult(text: str, num_tokens: int | None, stop_reason: VlmStopReason, usage: Any | None = None)`.
    *   `ApiImageStreamingRequestResult`: `ApiImageStreamingRequestResult(text: str, num_tokens: int | None, usage: Any | None = None)`.

*   Ensure `PictureDescriptionApiOptions` includes `usage_response_key` field, defaulting to 'usage', forwarding it to `api_image_request`.

*   In `PictureDescriptionBaseModel`, store the usage dict in picture description metadata under `docling__usage`.

*   In `ApiVlmModel.process_images`, expose the usage dict from API responses on predictions. Use `VlmStopReason.UNSPECIFIED` for streaming results' stop_reason.

*   In `ApiVlmEngine.predict_batch`, include 'num_tokens' and 'usage' in the output's `.metadata` dict from the API response.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.