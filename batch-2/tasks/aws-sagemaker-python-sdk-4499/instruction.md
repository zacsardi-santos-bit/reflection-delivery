Implement a structured data object to represent hub content summaries, aligning with AWS API response structures. Introduce utility functions to convert API responses into these structured objects and add functionality to inspect and tag model versions for deprecation or vulnerabilities.

*   Define the `CuratedHubUnsupportedFlag` enum in `src/sagemaker/jumpstart/curated_hub/types.py`:
    *   Extend `str` and `Enum`.
    *   Include members: `DEPRECATED_VERSIONS='deprecated_versions'`, `TRAINING_VULNERABLE_VERSIONS='training_vulnerable_versions'`, `INFERENCE_VULNERABLE_VERSIONS='inference_vulnerable_versions'`.

*   Define the `HubContentSummary` dataclass in `src/sagemaker/jumpstart/curated_hub/types.py`:
    *   Include fields: `hub_content_arn`, `hub_content_name`, `hub_content_version`, `hub_content_type`, `document_schema_version`, `hub_content_status`, `creation_time`, `hub_content_description` (default `None`), `hub_content_search_keywords` (default `None`).
    *   Ensure equality comparison between instances.

*   Implement `summary_from_list_api_response` in `src/sagemaker/jumpstart/curated_hub/types.py`:
    *   Accept a single dict from an API response.
    *   Return a `HubContentSummary` object.
    *   Map API keys to dataclass fields, default missing keys to `None`.
    *   Ensure 'Model' maps to `HubContentType.MODEL`.

*   Implement `summary_list_from_list_api_response` in `src/sagemaker/jumpstart/curated_hub/types.py`:
    *   Accept a dict with 'HubContentSummaries' key.
    *   Return a list of `HubContentSummary` objects.

*   Implement `find_unsupported_flags_for_model_version` in `src/sagemaker/jumpstart/curated_hub/utils.py`:
    *   Accept parameters: `model_id`, `version`, `region`, `session`.
    *   Call `verify_model_region_and_return_specs` with specified arguments.
    *   Return a list of `CuratedHubUnsupportedFlag` values based on `specs` attributes.

*   Implement `find_deprecated_vulnerable_flags_for_hub_content` in `src/sagemaker/jumpstart/curated_hub/utils.py`:
    *   Accept parameters: `hub_name`, `hub_content_name`, `region`, `session`.
    *   Call `session.list_hub_content_versions` with specified arguments.
    *   Filter versions without JumpStart model ID and version keywords.
    *   Aggregate flags using `find_unsupported_flags_for_model_version`.
    *   Return a list of dicts with flag information.

*   Update `_get_jumpstart_models_in_hub` in `CuratedHub` class (`src/sagemaker/jumpstart/curated_hub/curated_hub.py`):
    *   Return a list of `HubContentSummary` objects.
    *   Use `summary_list_from_list_api_response` for conversion.
    *   Filter entries with JumpStart model ID and version keywords.

*   Update `_determine_models_to_sync` in `CuratedHub` class:
    *   Accept a dict mapping hub content names to `HubContentSummary` objects.
    *   Access version via `.hub_content_version` attribute.

*   Ensure handling of AWS API responses uses correct field names: 'HubContentName', 'HubContentVersion', 'HubContentSearchKeywords'. Update any old field name usage.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.