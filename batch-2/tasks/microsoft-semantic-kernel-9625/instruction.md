Implement a text-to-audio service for both standard and Azure-hosted deployments, following the conventions of existing media services. Ensure the service reads configurations from environment variables, with the option to override via constructor parameters. Handle missing or invalid configurations by raising appropriate errors.

*   Define `TextToAudioClientBase` in `semantic_kernel/connectors/ai/text_to_audio_client_base.py`:
    *   Declare abstract method `get_audio_contents(text, settings, **kwargs) -> list[AudioContent]`.
    *   Implement `get_audio_content(text, settings, **kwargs) -> AudioContent` to return the first element from `get_audio_contents`.

*   Implement `OpenAITextToAudio` in `semantic_kernel/connectors/ai/open_ai/services/open_ai_text_to_audio.py`:
    *   Initialize using `OPENAI_TEXT_TO_AUDIO_MODEL_ID` from the environment or `ai_model_id` parameter.
    *   Raise `ServiceInitializationError` with specific messages for invalid or missing model ID, and missing API key.
    *   Support `from_dict(settings)` and `to_dict()` for serialization.
    *   Implement `get_audio_contents(text)` to return a list with one `AudioContent` object.
    *   Implement `get_prompt_execution_settings_class()` returning `OpenAITextToAudioExecutionSettings`.

*   Implement `AzureTextToAudio` in `semantic_kernel/connectors/ai/open_ai/services/azure_text_to_audio.py`:
    *   Initialize using `AZURE_OPENAI_TEXT_TO_AUDIO_DEPLOYMENT_NAME` from the environment.
    *   Raise `ServiceInitializationError` for missing deployment name, API key, endpoint/base_url, and non-HTTPS endpoint.
    *   Support `from_dict(settings)` with specific fields and ensure correct attribute assignments.
    *   Implement `get_audio_contents(text)` to return a list with one `AudioContent` object.
    *   Implement `get_prompt_execution_settings_class()` returning `OpenAITextToAudioExecutionSettings`.

*   Define `OpenAITextToAudioExecutionSettings` in `semantic_kernel/connectors/ai/open_ai/prompt_execution_settings/open_ai_text_to_audio_execution_settings.py`.

*   Update `AzureOpenAISettings` and `OpenAISettings` to include fields for reading text-to-audio configurations from environment variables.

*   Ensure the following classes and methods are importable from `semantic_kernel.connectors.ai.open_ai`:
    *   `AzureAudioToText`, `OpenAIAudioToText`, `OpenAIAudioToTextExecutionSettings`, `OpenAITextToImage`, `OpenAITextToImageExecutionSettings`, `AzureTextToAudio`, `OpenAITextToAudio`, `OpenAITextToAudioExecutionSettings`.

*   Ensure `AudioContent` is importable from `semantic_kernel.contents`.

*   Implement `get_prompt_execution_settings_class()` in `OpenAIAudioToText` and `OpenAITextToImage` to return their respective execution settings classes.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.