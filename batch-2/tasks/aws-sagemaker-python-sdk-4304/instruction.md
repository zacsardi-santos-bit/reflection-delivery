Implement automatic inference of model identifiers and versions for JumpStart resources in the SageMaker SDK. Ensure that the SDK can read tags from endpoints and training jobs to configure predictors and estimators without requiring manual input of model identifiers. Update the SDK to support inference-component-based endpoints and expose a utility function for AWS partition determination.

Requirements:

*   Update `aws_partition` function in `sagemaker/utils.py`:
    *   Rename from `_aws_partition` to `aws_partition`.
    *   Maintain behavior: return 'aws', 'aws-cn', 'aws-us-gov', 'aws-iso', or 'aws-iso-b' based on the region.

*   Add constants in `sagemaker/jumpstart/constants.py`:
    *   `EXTRA_MODEL_ID_TAGS`: Collection of tag key names for model ID aliases.
    *   `EXTRA_MODEL_VERSION_TAGS`: Collection of tag key names for model version aliases.

*   Implement `get_jumpstart_model_id_version_from_resource_arn` in `sagemaker/jumpstart/utils.py`:
    *   Signature: `(resource_arn: str, sagemaker_session) -> tuple[str | None, str | None]`.
    *   Use `session.list_tags(arn)` to find model ID and version from tags.
    *   Return a 2-tuple of model ID and version, or `None` for conflicts.

*   Create `sagemaker/jumpstart/session_utils.py` with functions:
    *   `get_model_id_version_from_training_job`: Infer model ID/version from training job tags.
        *   Construct ARN and call `get_jumpstart_model_id_version_from_resource_arn`.
        *   Raise `ValueError` if inference fails.
    *   `_get_model_id_version_from_model_based_endpoint`: Infer from non-inference-component endpoints.
        *   Raise `ValueError` if `inference_component_name` is not `None`.
    *   `_get_model_id_version_from_inference_component_endpoint_with_inference_component_name`: Infer from specified inference component.
    *   `_get_model_id_version_from_inference_component_endpoint_without_inference_component_name`: Discover and infer from single inference component.
    *   `get_model_id_version_from_endpoint`: Infer model ID/version from endpoint, handle inference components.

*   Update `Session` methods in `sagemaker/session.py`:
    *   `is_inference_component_based_endpoint`: Determine if endpoint uses inference components.
    *   `list_and_paginate_inference_component_names_associated_with_endpoint`: List inference components, handle pagination.

*   Modify `retrieve_default` in `sagemaker/predictor.py`:
    *   Infer model ID/version when not supplied using `get_model_id_version_from_endpoint`.
    *   Raise `ValueError` if inference fails.

*   Update `JumpStartEstimator.attach`:
    *   Infer model ID/version when not provided using `get_model_id_version_from_training_job`.
    *   Propagate `ValueError` if inference fails.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.