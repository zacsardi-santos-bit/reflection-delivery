Enhance the document conversion service client to improve error handling, option serialization, delivery target selection, and schema mismatch diagnostics. Implement structured failure information, optimize request payloads, and ensure clear exception handling for mismatched schemas.

*   Implement structured failure information:
    *   Add `FailureCategory` enumeration in `docling/datamodel/service/responses.py` with at least `INTERNAL`.
    *   Add `FailurePhase` enumeration in `docling/datamodel/service/responses.py` with at least `ORCHESTRATION`.
    *   Create `PublicFailureInfo` Pydantic model in `docling/datamodel/service/responses.py` with fields: `category`, `message`, `retryable`, and `phase`.
    *   Create `TaskFailureResult` Pydantic model in `docling/datamodel/service/responses.py` with fields: `failure` and `kind` (default 'TaskFailureResult').
    *   Extend `TaskStatusResponse` model in `docling/datamodel/service/responses.py` with an optional `failure` field of type `PublicFailureInfo`.

*   Improve exception handling:
    *   Add `TaskExecutionError` exception in `docling/service_client/exceptions.py` with a message string.
    *   Add `ResponseSchemaMismatchError` exception in `docling/service_client/exceptions.py` as a subclass of `ServiceError` with attributes: `status_code`, `detail`, and `message` ('Response schema mismatch — client and server versions may differ').
    *   Update `_raise_for_result_404` in `DoclingServiceClient` to raise `TaskExecutionError` when the task status is 'failure'.
    *   Modify `_fetch_result_response` in `DoclingServiceClient` to raise `TaskExecutionError` for `TaskFailureResult` JSON payloads.

*   Optimize option serialization:
    *   Add `_serialize_convert_options` method in `DoclingServiceClient` to serialize `ConvertDocumentsRequestOptions` excluding default and None values.
    *   Ensure `ConvertDocumentsRequestOptions` supports `model_dump(mode='json', exclude_defaults=True, exclude_none=True)` without warnings.

*   Implement automatic delivery target selection:
    *   Modify `_submit_convert_task` in `DoclingServiceClient` to accept a `target` parameter and include 'target_type': 'inbody' for `InBodyTarget`.
    *   Update `submit_and_retrieve_each` in `DoclingServiceClient` to handle optional `target` parameter with presigned-first fallback logic.

*   Enhance schema mismatch diagnostics:
    *   Update `_fetch_convert_result_payload` in `DoclingServiceClient` to raise `ResponseSchemaMismatchError` for unexpected JSON shapes.
    *   Modify `_fetch_presigned_result_async` in `DoclingServiceClient` to raise `ResponseSchemaMismatchError` for unexpected JSON shapes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.