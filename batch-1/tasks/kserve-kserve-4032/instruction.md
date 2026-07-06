Add a dedicated resource configuration section to the inference service configuration to specify default CPU and memory requests and limits for serving containers. Update all serving runtime types to use these defaults from the configuration object. Ensure the Python SDK includes a corresponding model for this new configuration.

*   Modify the InferenceServicesConfig struct:
    *   Add a Resource field of type ResourceConfig.
    *   Ensure ResourceConfig struct includes string fields: CPULimit, MemoryLimit, CPURequest, and MemoryRequest.
    *   Location: `pkg/apis/serving/v1beta1/inference_service_types.go`.

*   Update the Default method for all serving runtime types:
    *   Accept a *InferenceServicesConfig parameter.
    *   Use the Resource field from the config to set default CPU and memory resource requests and limits.
    *   Applicable to: custom predictor, custom explainer, custom transformer, HuggingFaceSpec, LightGBMSpec, ONNXRuntimeSpec, PaddleServerSpec, PMMLSpec, SKLearnSpec, TFServingSpec, TorchServeSpec, TritonSpec, XGBoostSpec.
    *   Method signature: `Default(config *InferenceServicesConfig)`.

*   Ensure that when DefaultInferenceService is invoked with an InferenceServicesConfig containing a Resource field, the resulting container's resource requirements reflect the config values.

*   Update the Python SDK:
    *   Create a V1beta1ResourceConfig model class in `python/kserve/kserve/models/v1beta1_resource_config.py`.
        *   Must be instantiable with at least cpu_limit and memory_limit string parameters.
    *   Modify the V1beta1InferenceServicesConfig class in `python/kserve/kserve/models/v1beta1_inference_services_config.py` to include a resource field of type V1beta1ResourceConfig.

*   Ensure resource defaults for inference service components are driven by the configuration object, not a globally-mutable variable.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.