Implement a configuration class for running NVIDIA inference model servers as sidecar containers in Flyte task pods. Create a credentials class for NGC authentication details, ensuring both are importable from the inference plugin's top-level namespace.

*   Implement the `NIMSecrets` class:
    *   Require `ngc_image_secret`, `ngc_secret_key`, and `secrets_prefix` as non-optional arguments.
    *   Raise `TypeError` if any of these fields are missing during instantiation.
    *   Export `NIMSecrets` from `flytekitplugins.inference`.

*   Implement the `NIM` class:
    *   Accept a `secrets` parameter of type `NIMSecrets`.
    *   Include optional parameters: `image`, `mem`, `port`, `hf_repo_ids`, `lora_adapter_mem`, and `env`.
    *   Default values:
        *   `image`: "nvcr.io/nim/meta/llama3-8b-instruct:1.0.0"
        *   `mem`: "20Gi"
        *   `port`: 8000
        *   `cpu`: 1
        *   `gpu`: 1
        *   `shm_size`: "16Gi"
        *   `health_endpoint`: "v1/health/ready"
    *   Set `pod_template.pod_spec.image_pull_secrets[0].name` to `NIMSecrets.ngc_image_secret`.
    *   Configure an init container:
        *   Set `env[0].name` to "NGC_API_KEY" and `env[0].value` to `f"$({secrets_prefix}{ngc_secret_key})".upper()`.
        *   Set `image` to the supplied `image` parameter.
        *   Set `resources.requests['memory']` to the supplied `mem` parameter.
        *   Set `ports[0].container_port` to the supplied `port` parameter.
    *   Expose default attributes:
        *   `base_url`: "http://localhost:8000"
        *   `_cpu`: 1
        *   `_gpu`: 1
        *   `_health_endpoint`: "v1/health/ready"
        *   `_mem`: "20Gi"
        *   `_shm_size`: "16Gi"
    *   Handle LoRA configuration:
        *   Raise `ValueError` if `hf_repo_ids` is provided without `lora_adapter_mem` with the message "Memory to allocate to download LoRA adapters must be set."
        *   Raise `ValueError` if `hf_repo_ids` is provided without "NIM_PEFT_SOURCE" in `env` with the message "NIM_PEFT_SOURCE environment variable must be set."
        *   Configure a LoRA download container if `hf_repo_ids`, `lora_adapter_mem`, and `env` containing "NIM_PEFT_SOURCE" are provided:
            *   Set `name` to "download-loras".
            *   Set `resources.requests['memory']` to `lora_adapter_mem`.
            *   Include all `hf_repo_ids` in `command[2]`.

*   Export `NIM` from `flytekitplugins.inference`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.