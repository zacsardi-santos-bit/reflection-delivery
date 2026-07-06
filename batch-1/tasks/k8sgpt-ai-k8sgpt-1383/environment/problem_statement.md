## Description

The cloud AI backend currently supports several Amazon-hosted AI models, but it lacks support for Amazon's newer generation of models that use a different API request and response format. Additionally, the model configuration does not track which specific model is active, there is no routing logic to select the right request format based on the model being used, and there is no validation to reject requests for unsupported models.

## Expected Behavior

- The model configuration should store the name of the model being used, so it can be referenced during request construction.
- There should be a dedicated method to build requests in the format expected by the newer Amazon model family (using an inference configuration block and a structured messages array), separate from the existing method used for older Amazon models.
- The general-purpose Amazon completion method should automatically route to the appropriate format based on the model name — newer models (whose names indicate they belong to the newer family) should use the new format, while older models use the existing format.
- When an unrecognized model name is provided, the system should return a clear error indicating which model is not supported, rather than silently using the wrong format.
- A response parser for the newer Amazon model family should be provided, capable of extracting the generated text from the nested response structure that family uses. If the response contains no content, the parser should return an empty string without error.

## Why This Matters

Without this support, users of k8sgpt who configure it to use the newer Amazon AI models will either get an error or receive malformed requests sent to the API. This change makes the tool work correctly with the latest Amazon-hosted models while also improving robustness by validating model names upfront.
