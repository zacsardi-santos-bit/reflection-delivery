Implement the AzureOpenAITextToAudioService class to support text-to-audio generation using Azure OpenAI. Ensure it integrates seamlessly with existing service abstractions and supports voice and format selection. Register the service using dependency injection and handle errors for unsupported configurations.

*   Implement the AzureOpenAITextToAudioService class:
    *   Must implement ITextToAudioService.
    *   Constructor signature: `AzureOpenAITextToAudioService(string deploymentName, string endpoint, string apiKey, string? modelId = null, HttpClient? httpClient = null, ILoggerFactory? loggerFactory = null)`.
    *   Throw ArgumentNullException if deploymentName is null.
    *   Throw ArgumentException if deploymentName is an empty string or whitespace.
    *   Set Attributes["ModelId"] to the provided modelId.
    *   Set Attributes["DeploymentName"] to the provided deploymentName.

*   Implement the GetAudioContentsAsync method:
    *   Signature: `GetAudioContentsAsync(string text, PromptExecutionSettings? executionSettings = null, Kernel? kernel = null, CancellationToken cancellationToken = default) -> Task<IReadOnlyList<AudioContent>>`.
    *   Return a list where the first element's Data property contains the raw audio bytes from the Azure OpenAI API.
    *   Throw NotSupportedException if the voice in execution settings is empty, whitespace, or not in the supported voices: "alloy", "echo", "fable", "onyx", "nova", "shimmer".
    *   Throw NotSupportedException if the response format is unsupported; supported formats include: "wav", "opus", "flac", "aac", "pcm".
    *   Include "voice", "response_format", and "model" fields in the JSON request body.
    *   Determine the model using the priority: constructor modelId > AzureOpenAITextToAudioExecutionSettings.ModelId > deploymentName.
    *   Use HttpClient.BaseAddress for requests if set, otherwise use the constructor endpoint.

*   Implement extension methods for dependency injection:
    *   AddAzureOpenAITextToAudio on IKernelBuilder:
        *   Signature: `AddAzureOpenAITextToAudio(this IKernelBuilder builder, string deploymentName, string endpoint, string apiKey) -> IKernelBuilder`.
        *   Register AzureOpenAITextToAudioService as ITextToAudioService.
    *   AddAzureOpenAITextToAudio on IServiceCollection:
        *   Signature: `AddAzureOpenAITextToAudio(this IServiceCollection services, string deploymentName, string endpoint, string apiKey) -> IServiceCollection`.
        *   Register AzureOpenAITextToAudioService as ITextToAudioService.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.