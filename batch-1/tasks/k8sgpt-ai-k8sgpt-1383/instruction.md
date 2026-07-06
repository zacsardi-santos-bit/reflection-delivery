Implement support for Amazon's newer generation of hosted AI models in the k8sgpt Bedrock integration. Update the model configuration to track the active model, and create logic to select the correct request format based on the model. Ensure validation of model names and provide a response parser for the new models.

*   Update the BedrockModelConfig struct:
    *   Add a `ModelName string` field to the existing fields: `MaxTokens int`, `Temperature float32`, and `TopP float32`.

*   Implement request building methods for AmazonCompletion:
    *   Create `GetDefaultCompletion` method:
        *   Signature: `(a *AmazonCompletion) GetDefaultCompletion(ctx context.Context, prompt string, modelConfig BedrockModelConfig) ([]byte, error)`
        *   Return a JSON-encoded request body with:
            *   `inputText` field formatted as `"\n\nUser: {prompt}"`
            *   `textGenerationConfig` object with fields:
                *   `maxTokenCount` from `modelConfig.MaxTokens`
                *   `temperature` from `modelConfig.Temperature`
                *   `topP` from `modelConfig.TopP`
    *   Create `GetNovaCompletion` method:
        *   Signature: `(a *AmazonCompletion) GetNovaCompletion(ctx context.Context, prompt string, modelConfig BedrockModelConfig) ([]byte, error)`
        *   Return a JSON-encoded request body with:
            *   `inferenceConfig` object with fields:
                *   `max_new_tokens` from `modelConfig.MaxTokens`
                *   `temperature` from `modelConfig.Temperature`
                *   `topP` from `modelConfig.TopP`
            *   `messages` array where the first element's `content` array's first item has a `text` field set to the input prompt

*   Update the AmazonCompletion.GetCompletion method:
    *   Signature: `(a *AmazonCompletion) GetCompletion(ctx context.Context, prompt string, modelConfig BedrockModelConfig) ([]byte, error)`
    *   Validate the model name using `isModelSupported`:
        *   Return an error with message `"model {modelName} is not supported"` if the model is unsupported.
    *   Route to the appropriate completion method:
        *   Use `GetNovaCompletion` if the model name contains "nova".
        *   Use `GetDefaultCompletion` otherwise.

*   Implement the isModelSupported function:
    *   Signature: `isModelSupported(modelName string) bool`
    *   Return `true` for supported models including "anthropic.claude-v2", "amazon.nova-pro-v1:0", and "amazon.titan-text-express-v1".
    *   Return `false` for unrecognized model names.

*   Add the NovaResponse struct and its method:
    *   Implement `ParseResponse` method:
        *   Signature: `(a *NovaResponse) ParseResponse(rawResponse []byte) (string, error)`
        *   Parse a JSON response with the expected format:
            *   Extract and return `content[0].text`.
            *   Return an empty string if the `content` array is empty.
            *   Return an error for invalid JSON input.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.