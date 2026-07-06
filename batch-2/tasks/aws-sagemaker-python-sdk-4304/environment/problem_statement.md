## Description

When working with endpoints or training jobs that were originally deployed or run using JumpStart, users currently have to manually supply the model identifier and version to retrieve a predictor or attach to a training job. This is inconvenient because JumpStart already tags these resources with the model identity at creation time — the SDK should be able to read those tags automatically and configure itself without requiring the user to supply information it can already infer.

Additionally, the SDK does not yet support the newer style of endpoint that uses inference components (rather than traditional model-based hosting), so predictor retrieval fails for such endpoints even when they were created by JumpStart.

## Expected Behavior

- When retrieving a predictor for an existing endpoint, the model identifier should be inferred automatically from the endpoint's tags if it is not explicitly provided. If the endpoint uses inference components, the SDK should handle this correctly, discovering the appropriate inference component automatically when there is exactly one, or requiring the user to specify it when there are multiple.
- When attaching to an existing training job, the model identifier should be inferred from the training job's tags if it is not explicitly provided.
- An error should be raised when the model identity cannot be inferred (e.g., the resource was not created by JumpStart or has no recognizable tags).
- A utility function for determining the AWS partition from a region name should be available as part of the public API.

## Why This Matters

These improvements reduce friction for users who want to resume working with previously deployed JumpStart resources without needing to track and re-supply the original model identifier. They also ensure compatibility with inference-component-based endpoints, which are becoming more common.
