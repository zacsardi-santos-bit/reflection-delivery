## Description

MLflow's automatic tracing for the AWS Bedrock service does not currently emit spans in a format that conforms to the OpenTelemetry Generative AI semantic conventions standard. When a developer enables Bedrock autologging, the recorded spans use MLflow-specific attribute keys, making it difficult to interoperate with other observability tools that expect the standard GenAI attribute naming.

## Expected Behavior

- When Bedrock autologging is enabled and the OpenTelemetry GenAI semantic convention mode is turned on, the recorded span for a model inference call should use the standard attribute keys for operation name, model identifier, inference parameters (temperature, max tokens, top-p), input/output messages, and token usage.
- Input and output messages should be serialized as structured JSON arrays where each message has a role and a list of typed content parts (each with a type indicator and content value).
- Tool call content in messages should be represented with a dedicated type indicating a tool invocation, including the tool's ID, name, and arguments.
- Tool result content should be represented with a dedicated type indicating a tool response, including the tool call ID and the result as a JSON string.
- Tool definitions sent with the request should be captured under the appropriate attribute key, stored flat without wrapping them inside an extra nesting layer.
- System instructions should be captured as a serialized array of typed content parts.
- Images in messages should be converted to a base64-encoded blob representation with modality, MIME type, and content fields. If the image bytes are already encoded as a string, they should be passed through unchanged.
- When this standardized mode is active, span attributes should not include any proprietary MLflow-prefixed keys.

## Why This Matters

Aligning Bedrock traces with the OpenTelemetry GenAI semantic conventions allows teams to use standard observability pipelines and tooling to analyze AI model interactions, compare traces across different providers, and maintain consistent telemetry schemas in production systems.
